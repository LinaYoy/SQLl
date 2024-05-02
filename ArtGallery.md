# Роговая Ангелина ИС-22/9-1
## Описание базы данных "Art Gallery"

![scheme](pictures/image(22).webp)

Эта база данных создана для хранения информации о картинной галерее. База данных содержит таблицы для хранения информации о выставке, авторах, изображениях и расположении картин.

### Таблица "Artists" (Художники)

![image(4)](pictures/image%20(4).png)

- **Artists_id**: уникальный идентификатор художника (PRIMARY KEY)
- **name**: имя художника (VARCHAR(30))
- **birthday**: дата рождения художника (TEXT)
- **genre**: жанр творчества художника (VARCHAR(30))

Пример вывода записей из таблицы "Artists":
```
SELECT * FROM Artists;
```

![image](pictures/image.png)


### Таблица "Exposed" (Экспозиции)

![image(5)](pictures/image%20(5).png)

- **Exposed_id**: уникальный идентификатор экспозиции (PRIMARY KEY)
- **hall**: название зала, где проводится экспозиция (VARCHAR(30))
- **paintings_id**: идентификатор картины, выставленной на экспозиции (FOREIGN KEY REFERENCES Paintings(id))

Пример вывода записей из таблицы "Exposed":

```
SELECT * FROM Exposed;
```

![image(1)](pictures/image (1).png)


### Таблица "Expositions" (Выставки)

![image(6)](pictures/image%20(6).png)

- **Expositions_id**: уникальный идентификатор выставки (PRIMARY KEY)
- **theme**: тема выставки (VARCHAR(30))
- **begining**: дата начала выставки (DATE)
- **ending**: дата окончания выставки (DATE)
- **exposed_id**: идентификатор экспозиции на выставке (FOREIGN KEY REFERENCES Exposed(id))

Пример вывода записей из таблицы "Expositions":

```
SELECT * FROM Expositions;
```

![image(2)](pictures/image(2).png)


### Таблица "Paintings" (Картины)

![image(7)](pictures/image%20(7).png)

- **Paintings_id**: уникальный идентификатор картины (PRIMARY KEY)
- **name**: название картины (VARCHAR(30))
- **date_of_writing**: дата написания картины (DATE)
- **artists_id**: идентификатор художника, создавшего картину (FOREIGN KEY REFERENCES Artists(id))

Пример вывода записей из таблицы "Paintings":

```
SELECT * FROM Paintings;
```

![image(3)](pictures/image(3).png)

## UNION

```
SELECT
name,
birthday, genre FROM Artists
UNION
SELECT name,
date_of_writing, NULL FROM Paintings;
```
![image(8)](pictures/image%20(8).png)

Результатом выполнения данного запроса будет таблица, содержащая следующие столбцы:
name (название художника или картины)
birthday (дата рождения художника, в противном случае NULL)
genre (жанр художника, в противном случае NULL)
date_of_writing (дата написания картины, в противном случае NULL)

Результирующая таблица будет содержать данные из таблиц Artists и Paintings, объединенные по столбцам name.

## ORDER BY

```
SELECT hall, paintings_id
FROM Exposed
ORDER BY paintings_id ASC;
```

![image(9)](pictures/image(9).png)

Этот запрос выбирает столбцы hall и paintings_id из таблицы Exposed и сортирует результат по столбцу paintings_id в порядке возрастания (ASC).

## HAVING 

```
SELECT Expositions.theme, COUNT() AS paintings_count
FROM Expositions
JOIN Exposed ON Expositions.exposed_id = Exposed.exposed_id
GROUP BY Expositions.theme
HAVING COUNT() = 1;
```

![image(10)](pictures/image(10).png)

Этот запрос объединяет таблицы Expositions и Exposed, группирует данные по теме экспозиции (столбец theme) и выводит количество картин в каждой экспозиции. Затем используется HAVING для фильтрации результатов и оставляются только те экспозиции, в которых количество картин больше 1.

## Вложенный запрос

```
SELECT name, 
       (SELECT name FROM Paintings WHERE artists_id = Artists.Artists_id) AS painting_name
FROM Artists
WHERE Artists_id IN (SELECT artists_id FROM Paintings WHERE date_of_writing > '1700');
```
![image(11)](pictures/image(11).png)

Этот запрос выбирает имена художников из таблицы Artists и использует вложенный запрос для выбора названий картин, написанных после 1700 года, из таблицы Paintings.

## Оконные функции

### Агрегатная функция

```
SELECT Expositions.theme, COUNT() AS paintings_count
FROM Expositions
JOIN Exposed ON Expositions.exposed_id = Exposed.exposed_id
GROUP BY Expositions.theme
HAVING COUNT() = 1;
```

![image(12)](pictures/image(12).png)

Этот запрос выбирает тему экспозиции из таблицы Expositions и подсчитывает количество картин, которые были выставлены в рамках каждой экспозиции. Затем результат группируется по теме экспозиции.

### Ранжирующая функция

```
SELECT name, birthday, RANK() OVER (ORDER BY birthday) AS artist_rank
FROM Artists;
```
![image(14)](pictures/14.png)

В результате выполнения этого запроса будут выведены имена художников, их даты рождения, а также их ранг по возрастанию даты рождения

## Функция смещения

```
SELECT *,
       ROW_NUMBER() OVER() AS row_number
FROM Paintings
ORDER BY Paintings_id
LIMIT 2 OFFSET 2;
```
![image(13)](pictures/image(13).png)

Результат этого запроса будет представлять собой таблицу с двумя строками данных из таблицы Paintings, начиная с третьей строки, отсортированных по столбцу Paintings_id. В результирующей таблице будет добавлен столбец row_number, содержащий порядковый номер каждой строки в результирующем наборе данных.

## JOIN

### INNER JOIN

```
SELECT * FROM Artists
INNER JOIN Paintings ON Artists.artists_id = Paintings.artists_id;
```
![image(15)](pictures/15.png)

Результирующая таблица будет содержать все столбцы из таблиц Artists и Paintings, где значения artists_id будут совпадать между этими двумя таблицами. Таким образом, можно получить информацию об художниках и их произведениях, которые написаны ими.

### LEFT JOIN

```
SELECT * FROM Artists
LEFT JOIN Paintings ON Artists.artists_id = Paintings.artists_id;
```
![image(16)](pictures/16.png)

Результатом данного запроса будет таблица, включающая все записи из таблицы Artists и только те записи из таблицы Paintings, которые имеют соответствующее значение artists_id в таблице Artists.

### RIGHT JOIN

```
SELECT * FROM Artists
RIGHT JOIN Paintings ON Artists.artists_id = Paintings.artists_id;
```
![image(17)](pictures/17.png)

Результатом данного запроса будет таблица, включающая все записи из таблицы Artists и только те записи из таблицы Paintings, которые имеют соответствующее значение artists_id в таблице Paintings.

### FULL OUTER JOIN

```
SELECT * FROM Artists
FULL OUTER JOIN Paintings ON Artists.artists_id = Paintings.artists_id;
```
![image(18)](pictures/18.png)

Результатом данного запроса будет таблица, содержащая все записи из таблицы Artists и все записи из таблицы Paintings, включая данные об обоих сущностях, при условии их соответствия по artists_id.

### CROSS JOIN

```
SELECT Artists.name, Paintings.name
FROM Artists
CROSS JOIN Paintings;
```
![image(19)](pictures/19.png)

Результатом такого запроса будет таблица, в которой будет отображено сочетание каждого имени художника из таблицы Artists с каждым именем картины из таблицы Paintings.

## CASE

```
SELECT name,
       CASE
           WHEN genre = 'Renaissance' THEN 'Baroque'
           WHEN genre = 'Impressionism' THEN 'Expressionism'
           ELSE 'Other'
       END AS artcategory
FROM Artists;
```

![image(21)](pictures/21.png)

В данном примере используется CASE-выражение для создания нового столбца "artcategory", который присваивает каждому художнику категорию искусства в зависимости от их жанра. 

## WITH

```
WITH Artists_paintings AS
	(SELECT Artists.* FROM Paintings
     INNER JOIN Artists ON Artists.Artists_id = Paintings.artists_id)
     
SELECT name, COUNT(name) as amount FROM Artists_paintings GROUP BY name;
```

![image(20)](pictures/20.png)

Результирующая таблица будет содержать два столбца: "name" - это имя художника, и "amount" - это количество картин, созданных каждым художником. Записи в таблице будут сгруппированы по именам художников, так что для каждого уникального имени будет отображено количество его картин. Таким образом, таблица покажет каждого художника и сколько у него картин в базе данных.
