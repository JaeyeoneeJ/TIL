## 1) 목적
- Enact CLI 4.1.8 프로젝트에서 TypeScript + Webpack 모두 인식하는 @ prefix alias 설정 (import a from '@/redux/store' 형태 사용 등)    

## 2) 문제 원인
- **enact.config.js가 로드되지 않음**  
    → package.json에 "enact": {...} 필드가 존재하면 enact.config.js는 완전히 무시됨.
- **package.json "enact.alias"의 상대경로 문제**  
      → Enact CLI는 alias 값을 절대경로로 변환하지 않고 webpack에 그대로 전달함.  
      → webpack은 alias 값이 절대경로여야 정상 동작하므로 "./src" 같은 상대경로는 무시됨.
- **근본 원인: @enact/dev-utils/option-parser.js 소스 문제**  
    → computed("alias", enact, config.alias) 로 값을 그대로 넘길 뿐 절대경로 변환 로직이 없음.

## 3) 해결방법
### 3-1) 변경 전
```js
// node_modules/@enact/cli/node_modules/@enact/dev-utils/option-parser.js

// 128번째 줄
config.alias = computed('alias', enact, config.alias);
```

### 3-2) 변경 후
```js
// node_modules/@enact/cli/node_modules/@enact/dev-utils/option-parser.js

// 128번째 줄
// config.alias = computed('alias', enact, config.alias);

const rawAlias = computed('alias', enact, config.alias);
config.alias = rawAlias
    ? Object.fromEntries(
        Object.entries(rawAlias).map(([k, v]) => [
            k,
            path.isAbsolute(v) ? v : path.resolve(config.context, v)
        ]))
    : rawAlias;
```

### 3-3) 최종 설정
- 아래 설정을 통해 TypeScript 타입 체크와 webpack 빌드 모두 @ prefix를 인식하게 됨.
- tsconfig.json
```json
{
	"compilerOptions": {
		"target": "ES2019",
		"module": "ESNext",
		"lib": ["dom", "es2019"], // DOM + ES2019 내장 객체(Intl 포함)

		"baseUrl": "./src", // alias 설정용 root path
		"paths": {
			"@/*": ["*"]
		},

		"resolveJsonModule": true,
		"esModuleInterop": true,

		"allowJs": false,
		// "checkJs": true, // 프로젝트 성격에 따라 js 사용비율이 있을 때, 타입 체크 필요 시 사용
		"jsx": "react",
		"moduleResolution": "node",
		"typeRoots": ["./src/types", "./node_modules/@types"],
		"outDir": "./outDir" // typescript 컴파일 시 에러 분리용
	},
	"include": ["src", "src/types"]
}
```

- package.json
```json
"enact": {
	"theme": "sandstone",
	"ri": {
		"baseSize": 24
	},

    // 추가
	"alias": {
		"@": "./src"
	}
},
```

## 4) 타인과 프로젝트 공유 시 자동 설정 방법
> **주의사항**
> - enact.config.js는 package.json에 “enact” 필드가 있으면 완전 무시됨
> - enact/cli 버전 변경 시 option-parser가 변경될 수 있으므로 재확인 필요
- 위의 사항을 다른 PC에서도 동일하게 진행하면 되나 번거로우므로 post-install을 활용한 자동화 방안을 권장함

### 4-1) 폴더 구조
- 해당 폴더 구조가 git에 올라가야 함.
```
patches
└── option-parser.js
scripts
└── copy-patch.js
package.json
```

### 4-2) patch 파일 생성
- 위의 option-parser.js를 npm install 시 post-install하기 위해 처리
```js
// patches/option-parser.js

const fs = require('fs');
const path = require('path');
const resolve = require('resolve');
const browserslist = require('browserslist');
const pkgRoot = require('./package-root');

const defaultTargets = [
	'>1%',
	'last 2 versions',
	'last 5 Chrome versions',
	'last 5 Firefox versions',
	'Firefox ESR',
	'not ie < 12',
	'not ie_mob < 12',
	'not dead'
];
const pkg = pkgRoot();
let enact = pkg.meta.enact || {};

const capitalize = name => name[0].toUpperCase() + name.slice(1);
const valid = v => v || v === false;

// Gently parse a file returning undefined on thrown errors
const gentlyParse = file => {
	try {
		return JSON.parse(fs.readFileSync(file, { encoding: 'utf8' }));
	} catch (e) {
		return undefined;
	}
};

// Resolve a filepath relative to a context and theme
const themeFile = (context, theme, file) => {
	const checks = [`@enact/${theme}/${file}`, `${theme}/${file}`];

	for (let i = 0; i < checks.length; i++) {
		try {
			return resolve.sync(checks[i], { basedir: context });
		} catch (e) {
			// do nothing
		}
	}
};

// Resolve a valid theme decorator relative filepath (eg. screentypes.json)
const decoFile = (dir, file) => {
	return [
		// Possible theme decorator locations
		path.join('ThemeDecorator', file),
		path.join(capitalize(path.basename(dir)) + 'Decorator', file)
	].find(f => fs.existsSync(path.join(dir, f)));
};

// Recursively resolves theme configuration details
const themeConfig = (context, theme) => {
	const pkgFile = themeFile(context, theme, 'package.json');
	if (pkgFile) {
		const meta = require(pkgFile);
		const cfg = meta.enact || {};
		cfg.name = meta.name;
		cfg.path = path.dirname(pkgFile);
		if (!cfg.screenTypes) cfg.screenTypes = decoFile(cfg.path, 'screenTypes.json');
		if (!cfg.fontGenerator) cfg.fontGenerator = decoFile(cfg.path, 'fontGenerator.js');
		if (cfg.theme) cfg.theme = themeConfig(cfg.path, cfg.theme);
		return cfg;
	}
};

// Computes the value of a config prop in a hierarchy of:
//     1. Environment variable level (ENACT_<prop>)
//     2. Local source level (within a package.json or local ThemeDecorator)
//     3. Extended theme level (explicitly or implicitly within extended theme)
const computed = (prop, app, theme) => {
	// Environment variables take top priority
	const envProp = 'ENACT_' + prop.toUpperCase();
	if (valid(process.env[envProp])) {
		if (/^([{[].*[}\]]|true|false)$/.test(process.env[envProp].trim())) {
			try {
				return JSON.parse(process.env[envProp]);
			} catch (e) {
				// do nothing
			}
		}
		return process.env[envProp];
	}
	// Local source level values take secondary priority
	if (valid(app[prop])) return app[prop];
	const selfThemeFiles = {
		screenTypes: decoFile(pkg.path, 'screenTypes.json'),
		fontGenerator: decoFile(pkg.path, 'fontGenerator.js')
	};
	if (valid(selfThemeFiles[prop])) return selfThemeFiles[prop];

	// Extended theme level values take tertiary priority
	const pathProps = ['isomorphic', 'template', 'screenTypes', 'fontGenerator'];
	const computeThemeProp = (p, cfg) => {
		if (valid(cfg[p])) {
			if (pathProps.includes(p)) return path.join(cfg.path, cfg[p]);
			return cfg[p];
		} else if (cfg.theme) {
			return computeThemeProp(p, cfg.theme);
		}
	};
	if (theme) return computeThemeProp(prop, theme);
};

const config = {
	// Project base directory.
	context: pkg.path,
	// Project name.
	name: pkg.meta.name,
	// Parse Enact metadata and apply options onto the config.
	applyEnactMeta: function (meta) {
		enact = Object.assign(enact, meta);

		// Parse the theme config tree for defaults
		config.theme = themeConfig(pkg.path, process.env.ENACT_THEME || enact.theme || 'sandstone');

		// Optional alternate entrypoint for isomorphic builds.
		config.isomorphic = computed('isomorphic', enact, config.theme);
		// Optional filepath to an alternate HTML template for html-webpack-plugin.
		config.template = computed('template', enact, config.theme);
		// Optional <title></title> value for HTML
		config.title = computed('title', enact, config.theme);
		// Optional flag whether to externalize the prerender startup js
		config.externalStartup = computed('externalStartup', enact, config.theme);
		// Optional object mapping of webpack path aliases to use when building
		//		config.alias = computed('alias', enact, config.alias); // origin

		// custom by jaeyeon.jeong
		const rawAlias = computed('alias', enact, config.alias);
		config.alias = rawAlias
			? Object.fromEntries(Object.entries(rawAlias).map(([k, v]) => [k, path.isAbsolute(v) ? v : path.resolve(config.context, v)]))
			: rawAlias;

		// Optional webpack node configuration value (see https://webpack.js.org/configuration/node/).
		config.nodeBuiltins = computed('nodeBuiltins', enact, config.theme);
		// Optional property to specify a version of NodeJS to target required polyfills.
		// True or 'current' will use active version of Node, otherwise will use a specified version number.
		config.node = computed('node', enact, config.theme);
		// Optional window condition(s) that indicate deeplinking and invalidate HTML prerender.
		config.deep = computed('deep', enact, config.theme);
		// Proxy target to use within the http-proxy-middleware during serving.
		config.proxy = computed('proxy', enact, config.theme) || pkg.meta.proxy;
		// Public path URL at which the app is served or destined to be hosted.
		config.publicUrl = computed('publicUrl', enact, config.theme) || pkg.meta.homepage;
		// Optionally force all LESS/CSS to be handled modularly, instead of solely having
		// the *.module.css and *.module.less files be processed in a modular context.
		config.forceCSSModules = computed('forceCSSModules', enact, config.theme);
		config.additionalModulePaths = computed('additionalModulePaths', enact, config.theme);

		// Resolve array of screenType configurations. When not found, falls back to any theme preset or sandstone.
		const screens = computed('screenTypes', enact, config.theme);
		config.screenTypes =
			(Array.isArray(screens) && screens) ||
			(typeof screens === 'string' &&
				((path.isAbsolute(screens) && gentlyParse(screens)) ||
					gentlyParse(path.join(pkg.path, screens)) ||
					gentlyParse(path.join(pkg.path, 'node_modules', screens)))) ||
			[];

		// Resolve the resolution independence settings from explicit settings or the resolved screenTypes definitions.
		const riConfig = computed('ri', enact, config.theme);
		config.ri = valid(riConfig) ? riConfig : config.screenTypes.reduce((r, s) => (s.base && { baseSize: s.pxPerRem }) || r, undefined);

		// Resolved filepath to fontGenerator. When not found, falls back to any theme preset or sandstone.
		const fontGenerator = computed('fontGenerator', enact, config.theme);
		config.fontGenerator =
			fontGenerator &&
			(path.isAbsolute(fontGenerator)
				? fontGenerator
				: [path.join(pkg.path, fontGenerator), path.join(pkg.path, 'node_modules', fontGenerator)].find(fs.existsSync));

		// Override theme's accent LESS variable value if desired. Private option; may be removed in future.
		// When used, creates a LESS variable override map, overriding '@moon-accent' and/or '@<theme>-accent'
		// values with the specified override. This allows a simple way to alter Enact spotlight color.
		if (enact.accent) {
			config.accent = { 'moon-accent': enact.accent };
			for (let t = config.theme; t; t = t.theme) {
				config.accent[t.name.replace('@enact/', '') + '-accent'] = enact.accent;
			}
		}
	},
	// Sets the browserslist default fallback set of browsers to the Enact default browser support list.
	setEnactTargetsAsDefault: function () {
		if (!browserslist.loadConfig({ path: pkg.path })) process.env.BROWSERSLIST = defaultTargets.join(',');
	}
};

Object.defineProperty(config, 'environment', {
	configurable: false,
	enumerable: true,
	get: function () {
		if (enact.environment) return enact.environment;

		let targets = browserslist.loadConfig({ path: pkg.path });
		if (targets) {
			if (typeof targets === 'string') targets = targets.split(/,\s*/);
			targets = targets.map(b => b.toLowerCase());
			if (targets.some(b => !b.startsWith('not') && b.includes('electron'))) {
				return 'electron-renderer';
			} else if (targets.every(b => !b.startsWith('not') && b.includes('node'))) {
				return 'node';
			} else {
				return 'web';
			}
		} else {
			return 'web';
		}
	}
});

config.applyEnactMeta();

module.exports = config;
```

- 위에서 언급한 128번째 줄만을 변경한 것이며, 전문임. 해당 파일 그대로 복사하여 붙여넣으면 됨.
    

```
// package.json
"scripts": {
	"serve": "enact serve -p 8091",
	"pack": "enact pack",
	"pack-p": "enact pack -p --locales=tv --content-hash",
	"watch": "enact pack --watch",
	"clean": "enact clean",
	"postinstall": "patch-package && node patches/option-parser.js"
},
```
- 위와 같이 설정할 경우, npm install 시 postinstall 명령어가 실행되며 option-parser가 실행

- 하지만 mac 등 unix 기반 명령어와 window 운영체제 명령어가 달라 실행이 되지 않을 수 있으므로 copy-patch script로 실행
```js
// scripts/copy-patch.js

const fs = require('fs');
const path = require('path');

const src = path.resolve(__dirname, '../patches/option-parser.js');
const dest = path.resolve(__dirname, '../node_modules/@enact/cli/node_modules/@enact/dev-utils/option-parser.js');

if (!fs.existsSync(dest)) {
	console.warn('[patch] destination not found, skipping:', dest);
	process.exit(0);
}

fs.copyFileSync(src, dest);
console.log('[patch] option-parser.js applied successfully');
```

- package.json 내 script 명령어 변경    
```json
"postinstall": "patch-package && node scripts/copy-patch.js"
```