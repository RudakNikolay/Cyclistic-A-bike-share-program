# Cyclistic: A bike share program
Данный проект является частью профессиональной сертификации 'Google Data Analytics' 

Cyclistic: программа проката велосипедов, включающая более 5800 велосипедов и 600 док-станций для зарядки электро-велосипедов.  

Также предлагая лежачие велосипеды, ручные трехколесные велосипеды и грузовые велосипеды, что делает прокат велосипедов более инклюзивным для людей с ограниченными возможностями.  
Инвалиды и водители, которые не могут использовать стандартный двухколесный велосипед.  
Большинство  выбирают традиционные велосипеды, но 8%  используют вспомогательные опции.  
Велосипедисты чаще ездят на велосипеде для отдыха, но около 30% используют их для езды на работу каждый день.

Проект нацелен на улучшение коммуникации с потребителями, лучшему пониманию из потребностей, выстраиванию маркетинговой политики для привлечения новых клиентов. Так же выжным параметром является сохранение и увеличение райдеров с подпиской.

Проект полностью выполнен с использованием R-studio, dерсия 2023.03.0+386.

Установка необходимых пакетов для раборы:
```
install.packages("tidyverse", "ggplot2", "rstudioapi", "ggmap")
library(tidyverse, ggplot2, rstudioapi, ggmap)  
```
Данные для анализа доспутны по [ссылке.](https://divvy-tripdata.s3.amazonaws.com/index.html)  
Загрузка данных в R с использование функуии read_csv пакета readr включенного в tidyverse.  
Важным является указание форматов для колонок с датами еще на этапе формирования дата-сета, что бы не иметь с этип проблем в будущем.
```
X202302_divvy_tripdata <- read_csv("Desktop/Rproject/202302-divvy-tripdata.csv", 
                                   col_types = cols(started_at = col_datetime(format = "%Y-%m-%d %H:%M:%S"), 
                                                    ended_at = col_datetime(format = "%Y-%m-%d %H:%M:%S")))
```
Создав дата-сет трансформирую его, дабавляя две колонки с помощью функции mutate.  
ride_length - высчитываю время поезки в секундах  
week_days - нахожу день недели начала поездки  
```
df <- X202302_divvy_tripdata
mutate (df, ride_length =  df$ended_at - df$started_at)
mutate (df, week_days = format(as.Date(df$started_at),"%A"))
```
С помошью функии filter очищаю дата-сет от записей с временемм поедки меньше минуты и от записей где не указаны координаты конца поездки.
```
df<- filter (df, ride_length > 0,!is.na(end_lat))
```
Получив очищенные данные создаю 2 агрегированные таблицы.  
В разрезе типа велосипеда и велосипедиства (подписчик / случайный райдер) расчитываю время всех поедок в секундах, время средней поезки и общее количество поездок. Аналогичные данные высчитываю в разрезе дней недели. 

```
agg_df <- df %>% 
  group_by(rideable_type, member_casual) %>% 
  summarise (sum = sum(ride_length), 
             avg = mean(ride_length),
             cnt = n()  )
```
| rideable_type | member_casual | sum           | avg            | cnt   |
|---------------|---------------|---------------|----------------|-------|
| classic_bike  | casual        | 18929502 secs | 1222.5201 secs | 15484 |
| classic_bike  | member        | 49799914 secs | 669.9931 secs  | 74329 |
| docked_bike   | casual        | 5540804 secs  | 2575.9200 secs | 2151  |
| electric_bike | casual        | 16565867 secs | 655.1658 secs  | 25285 |
| electric_bike | member        | 42996113 secs | 588.4156 secs  | 73071 |
```
agg_df_week <- df %>% 
  group_by(week_days) %>% 
  summarise (sum = sum(ride_length), 
             avg = mean(ride_length),
             cnt = n()  ) %>% 
  arrange(ordered(week_days, levels = c("Monday", "Tuesday", "Wednesday", "Thursday", "Friday","Saturday","Sunday")))
```
| week_days | sum           | avg           | cnt   |
|-----------|---------------|---------------|-------|
| Monday    | 21041495 secs | 675.2943 secs | 31159 |
| Tuesday   | 22224027 secs | 626.6644 secs | 35464 |
| Wednesday | 15538631 secs | 602.5761 secs | 25787 |
| Thursday  | 13602135 secs | 622.7228 secs | 21843 |
| Friday    | 14047113 secs | 651.8987 secs | 21548 |
| Saturday  | 19885015 secs | 819.7640 secs | 24257 |
| Sunday    | 27493784 secs | 908.5250 secs | 30262 |

Данные получены. Приступаю к построению графиков основынных на данных с помощью пакета ggplot2.  
График "Среднее время одной поездки в минутах"
```
ggplot(data = agg_df, aes(x = rideable_type, y = round(avg/60, digit = 0), 
                          group = member_casual)) +
  geom_col(aes(fill = member_casual), 
           position = "dodge") +
  geom_text(aes(label = round(avg/60, digit = 0), avg = avg + 0.05), 
            position = position_dodge(0.9), vjust = 0)+
  labs(title = "Среднее время одной поездки в минутах",
       x = "Разрез тип велосипеда, подписка",
       y = "Время поездок")
```











```

```


