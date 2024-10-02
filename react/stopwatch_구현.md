>아래 테스트 내용은 저의 github에 수록되어 있습니다. 테스트 앱의 확인이 필요한 경우, [stopwatch-test](https://github.com/JaeyeoneeJ/stopwatch)를 통해 프로젝트를 확인할 수 있습니다.

## 0. 개요
- 종종 프로젝트 또는 개발 명시 사항을 구현하기 위해 우리는 시간이 흘러가는 것을 피드백해주는, 마치 스톱워치와 유사한 기능을 구현해야 할 때가 있다.
- 나의 경우에는 특정 영상을 재생했을 때, 영상 플레이 시간이 아닌 실제 영상 앞에서 사용자가 시간을 얼마나 보냈는지를 측정하는 등 측정 로직이 필요했다.

- 불행하게도, 자바스크립트 내에서 측정하는 시간(setTimeout, setInterval 등)은 실제 시간과 정확하게 맞아 떨어지지 않는다. 자바스크립트는 브라우저의 힙 메모리를 사용하여 측정하기에 현재 세션이 과부하가 걸려 느려지거나 과도한 메모리를 사용하게 되면 시간이 틀어지기 시작한다. 즉, 자바스크립트 엔진에서 동일한 1000ms를 측정하더라도 사용환경 등에 따라 현실의 시간에서는 1000ms보다 큰 값으로 리턴받을 수 있기 때문이다.
- 만일 우리의 앱이 정확하게 시간을 측정해야 한다면, 나의 경우처럼 영상 시청 시간의 오차가 계속 증가하게 된다면 나비효과로 추후 폭풍처럼 측정한 시간이 틀어질 수 있다.
- 나의 경우에는 제한된 하드웨어(TV 등)에서 개발을 진행하고 있어, 모바일이나 일반 PC 환경과는 달리 메모리 등이 풍족하지 않기 때문에 이러한 현상이 더욱 재현되기 쉬웠다.
- 따라서, 나는 이 실제로 측정하는 시간을 스톱워치와 유사하게 구현하면서, 실제 시간과 일정 시간 이상 틀어졌을 때, 실제 시간으로 맞추는 로직이 필요했다.

## 1. 설계
- 그렇다면 어떻게 실제 시간과 틀어지지 않도록 구현할 수 있을까?
- 방법은 생각보다 간단하다.
	- (나의 경우) 첫 영상 시청 시작 시간과 영상 시청 종료시간을 구하는 방법
	- 특정 시간마다 실제 시간과 자바스크립트 엔진의 틀어진 시간을 감지하고 틀어졌을 때 강제로 실제 시간으로 끌어오는 방법
- 이 두 방법은 사용 결과에 대해 완전히 상반된 결과를 보여준다.
- 첫 번째 방법은 영상의 시청 종료 시간이 끝나야만 실제 시간 시청한 시간을 구하므로 결과에서 피드백을 받을 수 있지만, 두 번째 방법은 영상을 시청하고 있는 동안 실시간으로 유저에게 피드백을 줄 수 있다. 마치 스톱워치와 같이.
- 따라서 나는 내가 구현해야 하는 기능을 위해 두 번째 방법을 사용하여 스톱워치를 구현하기로 했다.

## 2. 기능 구현
- 만들 방법을 정했으니 우선 기능을 구현해보도록 하자.

- stopwatch의 기능을 구현하기 위해 useStopwatch라는 hook을 만들었다.
- useStopwatch의 주요 기능은 아래와 같다.
	- 시작
	- 멈춤
	- 리셋
- 상기 기능은 가장 기본적인 stopwatch의 기능이다.
- stopwatch는 시작 상태가 되면 status가 processing으로 변경되며, stop 상태 또는 초기 상태에서는 status가 stop으로 돌아간다. 리셋을 누르면 모든 상태가 초기 상태로 돌아간다.

- 또한 현재 stopwatch의 상태가 진행중인지를 판별하기 위해 status를 설정한다.
```jsx
export const STATUS = {
  PROCESSING: "processing",
  STOP: "stop",
};
```

- 나의 경우에는, 시간 기준 기록을 재는 기능이 필요했다. 만일 여러 유저가 같은 시간에 시작했을 때, 이 유저가 중간에 나갔다가 다시 들어왔을 때의 기록이 위의 stopwatch처럼 잰다면 이 기록은 쉬는시간을 무시하고 재기 때문에 처음부터 시간을 잰 유저가 불리한 상황이 발생할 것이다.
- 따라서 시작한 시간을 기준으로 stopwatch가 작동되는 방식을 추가적으로 구현하기 위해, 시간을 받는 `currentDate` prop을 추가하였다.
- 실제 시간과의 구분을 주기 위해 `realTimeCheckMode` prop을 추가하였다.

- 기본적으로 시간을 재는 방법은 setInterval을 사용하였다.
```jsx
  useEffect(() => {
    let intervalId = 0;

    if (status === STATUS.PROCESSING) {
      intervalId = window.setInterval(() => {
        setSeconds((prev) => {
          return prev + INTERVAL / MILLISEC_PER_SECOND;
        });
      }, INTERVAL); // 0.01s마다
    }

    return () => {
      // cleanup 함수로, 컴포넌트가 사라질 때 실행됨
      window.clearInterval(intervalId);
    };
  }, [status]); // status가 바뀔 때 useEffect에 있는 함수 실행
```
- 이렇게 코드를 작성하면 위에서 언급한 실제시간에 대한 이슈가 발생할 수 있다. 즉, 시간이 틀어질 수 있다.
- 따라서, 현재 측정한 시간과 실제 시간의 오차가 1초 이상일 경우 실제 시간을 반영하는 로직이 추가로 필요하다.

```jsx
  useEffect(() => {
    if (status === STATUS.PROCESSING) {
      intervalRef.current = setInterval(() => {
        const currentTime = new Date();

        durationTimeRef.current = (currentTime - currentTimeRef.current) / 1000;

        setSeconds((prev) => {
          const newSeconds = prev + INTERVAL / MILLISEC_PER_SECOND;
          if (Math.abs(durationTimeRef.current - newSeconds) < 1 || realTimeCheckMode) {
            return newSeconds;
          } else {
            return Math.floor(durationTimeRef.current); // 실제 시간과 js 시간의 오차가 1초 이상 발생 시 실제 시간을 강제로 사용
          }
        });
      }, INTERVAL);
    }

    return () => {
      clearInterval(intervalRef.current);
    };
  }, [status, realTimeCheckMode]);
```

- 완성된 코드는 아래와 같다.

```jsx
import { useCallback, useEffect, useMemo, useRef, useState } from "react";
import { INTERVAL, MILLISEC_PER_SECOND } from "../utils/stopwatch/constants";

export const STATUS = {
  PROCESSING: "processing",
  STOP: "stop",
};

const useStopwatch = ({ currentDate = null, realTimeCheckMode = false } = {}) => {
  const [seconds, setSeconds] = useState(currentDate ? Math.floor((new Date() - currentDate) / 1000) : 0);
  const [status, setStatus] = useState(STATUS.STOP);
  const [laps, setLaps] = useState([]);

  const currentTimeRef = useRef(null);
  const durationTimeRef = useRef(null);

  const nextLap = useMemo(() => {
    return {
      id: laps.length + 1,
      label: "랩",
      lapTime: seconds - (laps[laps.length - 1]?.seconds ?? 0),
      seconds,
    };
  }, [seconds, laps]);

  const start = useCallback(() => {
    if (status !== STATUS.STOP) {
      return;
    }

    if (seconds === 0) {
      currentTimeRef.current = new Date();
    }
    setStatus(STATUS.PROCESSING);
  }, [status, seconds]);

  const stop = useCallback(() => {
    if (status !== STATUS.PROCESSING) {
      return;
    }

    setStatus(STATUS.STOP);
    clearInterval(intervalRef.current); // 멈춤 시 불필요한 setInterval이 돌지 않도록 적용
  }, [status]);

  const reset = useCallback(() => {
    // todo: 이미 상태가 정지이여야만 리셋 가능
    if (status !== STATUS.STOP) {
      setStatus(STATUS.STOP);
    }

    setSeconds(0);
    setLaps([]);
    clearInterval(intervalRef.current); // 리셋 시 불필요한 setInterval이 돌지 않도록 적용
  }, [status]);

  const record = useCallback(() => {
    if (status !== STATUS.PROCESSING) {
      return;
    }

    setLaps((prev) => [...prev, nextLap]); // 배열을 단순히 추가만 하면 안됨. 의존성은 주소를 참조하고 있어서 안 바뀜
  }, [status, nextLap]);

  useEffect(() => {
    if (currentDate) {
      currentTimeRef.current = currentDate;
    }
  }, [currentDate]);

  const intervalRef = useRef(null);

  useEffect(() => {
    if (status === STATUS.PROCESSING) {
      intervalRef.current = setInterval(() => {
        const currentTime = new Date();

        durationTimeRef.current = (currentTime - currentTimeRef.current) / 1000;

        setSeconds((prev) => {
          const newSeconds = prev + INTERVAL / MILLISEC_PER_SECOND;
          if (Math.abs(durationTimeRef.current - newSeconds) < 1 || realTimeCheckMode) {
            return newSeconds;
          } else {
            return Math.floor(durationTimeRef.current); // 실제 시간과 js 시간의 오차가 1초 이상 발생 시 실제 시간을 강제로 사용
          }
        });
      }, INTERVAL);
    }

    return () => {
      clearInterval(intervalRef.current);
    };
  }, [status, realTimeCheckMode]);

  return {
    seconds,
    status,
    laps,
    start,
    stop,
    reset,
    record,
    nextLap,
  };
};

export default useStopwatch;
```

- 나의 경우에는 랩타임을 재는 기능을 추가하였다.
- 기록한 시간 중, 특정 구간까지의 기록을 표시하면 좋겠다고 생각하여 추가하였다.

<img src="https://github.com/user-attachments/assets/3b30d5d6-f488-427c-a77c-be741b1196da" alt="stopwatch" />

- 랩타임도 잘 작동하는 것을 확인할 수 있다.