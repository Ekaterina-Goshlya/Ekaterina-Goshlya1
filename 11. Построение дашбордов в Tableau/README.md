# Построение дашбордов в Tableau

## Описание проекта
TED (от англ. technology, education, design — «технологии, образование, дизайн») — некоммерческий фонд, который проводит популярные конференции. На них выступают специалисты из разных областей и читают лекции на актуальные социальные, культурные и научные темы. 
В разное время на TED-конференциях выступали математик Бенуа Мандельброт, теоретик искусственного интеллекта Марвин Минский, спортсменка Дана Ньяд и основатель Google Ларри Пейдж. В истории TED также были неоднозначные и даже скандальные выступления. Например, в 2010 году на конференции выступил Рэнди Пауэлл с рассказом о псевдонаучной «вихревой математике», а в 2014 году в конференции TEDMED участвовала Элизабет Холмс — основательница печально известного стартапа Theranos.

## Описание данных
`tableau_project_data_1.csv`
`tableau_project_data_2.csv`
`tableau_project_data_3.csv`
`tableau_project_event_dict.csv`
`tableau_project_speakers_dict.csv`

Файлы `tableau_project_data_1.csv`, `tableau_project_data_2.csv`, `tableau_project_data_3.csv` хранят данные выступлений. У них одинаковая структура:
 - `talk_id` — идентификатор выступления;
 - `url` — ссылка на запись выступления;
 - `title` — название выступления;
 - `description` — краткое описание;
 - `film_date` — дата записи выступления;
 - `duration` — длительность в секундах;
 - `views` — количество просмотров;
 - `main_tag` — основная категория, к которой относится выступление;
 - `speaker_id` — уникальный идентификатор автора выступления;
 - `laughter_count` — количество раз, когда аудитория смеялась в ходе выступления;
 - `applause_count` — количество раз, когда аудитория аплодировала в ходе выступления;
 - `language` — язык, на котором велось выступление;
 - `event_id` — уникальный идентификатор конференции.
 
 Файл `tableau_project_event_dict.csv` — справочник конференций. Описание таблицы:
 - `conf_id` — уникальный идентификатор конференции;
 - `event` — название конференции;
 - `country` — страна проведения конференции.

 Файл `tableau_project_speakers_dict.csv` — справочник авторов выступления. Описание таблицы:
 - `author_id` — уникальный идентификатор автора выступления;
 - `speaker_name` — имя автора;
 - `speaker_occupation` — профессиональная область автора;
 - `speaker_description` — описание профессиональной деятельности автора.

## Задача проекта 
В этом проекте мы исследуем историю TED-конференций с помощью Tableau. 
- Создадим дашборд «История выступлений»
- Создадим дашборд «Тематики выступлений»
- Создадим дашборд «Авторы выступлений»
- Создадим дашборд на свободную тему
- Создадим презентацию

## Ссылка на проект и презентацию в Tableau:
https://public.tableau.com/app/profile/ekaterina.tkacheva2396/viz/Yandex_project_17212265763230/Story?publish=yes
