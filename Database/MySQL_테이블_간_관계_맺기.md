- MySQL과 같은 관계형 데이터베이스에서의 핵심은 테이블 간 적절히 관계를 맺어주면서 데이터 중복을 최소화하면서 구조를 짜임새 있게 만드는 것임
- 나의 경우, 동영상 원본 파일과 인코딩한 파일을 따로 관리하고 싶어 테이블을 2개 만들고, 이 두 테이블에 연관성을 부여하고자 함
- 아래와 같이 테이블이 있다고 가정해보겠음
```mysql
desc videos;
# +------------+--------------+------+-----+---------+----------------+
# | Field      | Type         | Null | Key | Default | Extra          |
# +------------+--------------+------+-----+---------+----------------+
# | id         | bigint       | NO   | PRI | NULL    | auto_increment |
# | title      | varchar(255) | NO   |     | NULL    |                |
# | voice_ko   | varchar(255) | YES  |     | NULL    |                |
# | voice_en   | varchar(255) | YES  |     | NULL    |                |
# | voice_thai | varchar(255) | YES  |     | NULL    |                |
# | bgm        | varchar(255) | YES  |     | NULL    |                |
# | video      | varchar(255) | NO   |     | NULL    |                |
# | thumbnail  | varchar(255) | YES  |     | NULL    |                |
# +------------+--------------+------+-----+---------+----------------+
```

- 우리가 인코딩한 파일이 있다는 것은 원본 파일이 있다는 의미이고, 이를 식별할 이름을 명시적으로 지을 필요가 있음
- 따라서 나는 title이 primary key와 같은 역할을 수행하면 좋겠음
- 기존에 이미 id 칼럼에 primary key가 부여되어 있기 때문에 id 칼럼에 그대로 primary key를 유지하면서 title 칼럼에 unique key를 부여하고자 함
- 아래와 같이 alter table 문을 사용하여 unique key를 추가하겠음
```mysql
ALTER TABLE videos
ADD UNIQUE KEY unique_title (title);
# Query OK, 0 rows affected (0.02 sec)
# Records: 0  Duplicates: 0  Warnings: 0
```

- 위 문장을 실행하면 title 칼럼에 unique key가 추가되고 id 칼럼은 그대로 primary key가 유지됨
- 위에서 unique_title은 videos 테이블에서 title 칼럼에 대한 unique constraint 이름임. unique constraint는 중복되지 않는 값을 갖는 것을 보장하는 제약 조건임. 이 경우, title 칼럼에 중복된 값이 입력될 경우 데이터베이스가 에러를 반환하게 됨. 즉, unique key의 이름을 식별하기 쉽게 지정한 것임
- 결과는 아래와 같음
```mysql
desc videos;
# +------------+--------------+------+-----+---------+----------------+
# | Field      | Type         | Null | Key | Default | Extra          |
# +------------+--------------+------+-----+---------+----------------+
# | id         | bigint       | NO   | PRI | NULL    | auto_increment |
# | title      | varchar(255) | NO   | UNI | NULL    |                |
# | voice_ko   | varchar(255) | YES  |     | NULL    |                |
# | voice_en   | varchar(255) | YES  |     | NULL    |                |
# | voice_thai | varchar(255) | YES  |     | NULL    |                |
# | bgm        | varchar(255) | YES  |     | NULL    |                |
# | video      | varchar(255) | NO   |     | NULL    |                |
# | thumbnail  | varchar(255) | YES  |     | NULL    |                |
# +------------+--------------+------+-----+---------+----------------+
```

- 인코딩한 정보가 담긴 새로운 테이블을 만들어보겠음.
```mysql
create table convertVideos (
	title varchar(255) not null,
	media_type varchar(20) not null,
	url varchar(255) not null,
	primary key (url),
	constraint fk_videos_title
	foreign key(title)
	references videos(title)
	on delete cascade
	on update cascade
);
```
- 새로 만든 테이블의 이름은 convertVideos이며 title, media_type, url 3개의 칼럼을 만들었음
- url은 primary key로 지정했으며 foreign key로 title 컬럼을 지정함. videos의 title 칼럼 값을 참조하게 하였음
- videos의 title 칼럼이 삭제 또는 수정되면 convertVideos에도 적용이 되게 하였음

- 이제 convertVideos에 데이터를 넣어보겠음
- 먼저 videos의 데이터를 조회함
```mysql
select title from videos;
# +----------------------------+
# | title                      |
# +----------------------------+
# | extraordinary_attorney_woo |
# | hello_world                |
# | my_love_from_the_star      |
# +----------------------------+
```

- 현재 videos table에 없는 title 값을 넣어보겠음
```mysql
INSERT INTO convertVideos (title, media_type, url) values ('test_title', 'video', 'test_url');
# ERROR 1452 (23000): Cannot add or update a child row: a foreign key constraint fails (`video_db`.`convertvideos`, CONSTRAINT `fk_videos_title` FOREIGN KEY (`title`) REFERENCES `videos` (`title`) ON DELETE CASCADE ON UPDATE CASCADE)
```
- 에러 메세지를 보면 자식 행에 대한 추가나 업데이트가 실패했으며 foreign key 제약 조건을 위반했다는 것을 알려줌
- 해당 테이블의 title 칼럼은 videos 테이블의 title 칼럼을 참조하도록 제약 조건이 설정되어 있음
- 하지만 insert 쿼리에서는 videos 테이블에 존재하지 않는 test_title을 참조하려고 함
- 여기서 우리는 videos 테이블에 test_title에 해당하는 레코드가 있는지를 확인할 필요가 있음

```mysql
select title from videos where title like 'test_title';
# Empty set (0.00 sec)
```

- 당연히 없음. 이처럼 title의 명칭이 잘못된 경우 방지할 수 있음
- 이번에는 videos table에 있는 title 값을 넣어보겠음
```mysql
INSERT INTO convertVideos (title, media_type, url) values ('hello_world', 'video', 'hello_world_test_url');
# Query OK, 1 row affected (0.01 sec)

select * from convertVideos;
# +-------------+------------+----------------------+
# | title       | media_type | url                  |
# +-------------+------------+----------------------+
# | hello_world | video      | hello_world_test_url |
# +-------------+------------+----------------------+
# 1 row in set (0.00 sec)
```

- 값이 정상적으로 들어간 것을 알 수 있음
- 또한 아래와 같이 연결되어 있는 테이블의 조회도 가능함
```mysql
select videos.*, convertVideos.media_type, convertVideos.url from convertVideos join videos on videos.title = convertVideos.title;
# +----+-------------+--------------------------+----------+------------+---------------------+-------+-----------+------------+----------------------+
# | id | title       | voice_ko                 | voice_en | voice_thai | bgm                 | video | thumbnail | media_type | url                  |
# +----+-------------+--------------------------+----------+------------+---------------------+-------+-----------+------------+----------------------+
# |  2 | hello_world | hello_world_voice_ko.wav | NULL     | NULL       | hello_world_bgm.wav |       | NULL      | video      | hello_world_test_url |
# +----+-------------+--------------------------+----------+------------+---------------------+-------+-----------+------------+----------------------+
# 1 row in set (0.00 sec)
```
- 결과물로 보일 테이블의 칼럼은 videos의 모든 칼럼과 convertVideos의 media_type, url을 가져오게 함
- 잘 작성된 것을 알 수 있음
