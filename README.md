# Друзья
Для создания функционала друзей необходимо создать 2 таблицы

1. Таблица пользователей ```Users```

```MySQL
create table users
(
	id int auto_increment
		primary key,
	email varchar(255) not null,
	createdAt timestamp default CURRENT_TIMESTAMP not null,
	constraint users_email_uindex
		unique (email)
);
```
2. Таблица друзей 
```MySQl
  create table friends
  (
    id int auto_increment
      primary key,
    user_id int null,
    friend_id int null,
    status enum('INACTIVE', 'REQUEST', 'ACTIVE') null,
    constraint friends_uindex
      unique (user_id, friend_id, status),
    constraint friend_id_fk
      foreign key (friend_id) references users (id),
    constraint user_id_fk
      foreign key (user_id) references users (id)
  );
```
3. Механизм работы следующий, при подачи заявки в друзья создаётся запись в таблице ```friends```, где ```user_id```- это ```id``` пользователя сделавшего запрос (Пользователь №1), а ```friend_id``` - ```id``` которому послали запрос (Пользователь №2). При подтверждении завяки в друзья пользователем №2 создаётся дополнительная запись в таблице ```friends```, где ```user_id``` - это ```id``` пользователя №2, а ```friend_id``` - ```id``` Пользователя №1. Так же при подтверждении заявки статус обоих записей меняется на ```ACTIVE``` 

``` Важно:``` в случае если количество записей станет слишком большим можно будет применить партиционирование таблиц

4. SQL-запрос на получение списка всех возможных друзей 2-го круга (друзья друзей) для пользователя № 1:
```MySQl
SELECT friend_id FROM friends WHERE user_id IN (SELECT friend_id FROM friends WHERE user_id = 2 AND status = 'ACTIVE') AND status = 'ACTIVE' AND friend_id NOT IN (SELECT friend_id FROM friends WHERE user_id = 2) AND friend_id != 2

```
5. EXPLAIN находиться в файле ```EXPLAIN.tsv```
