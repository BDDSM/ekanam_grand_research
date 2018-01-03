

Итак, в конечном счёте, мы хотим сделать две вещи:

1. Построить кучу красивых визуализаций и понять что вообще в принципе происходит внутри нашего сообщества. В этой части исследования мы посмотрим кто кому и сколько ставит лайков, кто кого добавляет в друзья и кто на какие группы подписывается. При этом без графов здесь явно не обойдётся.
2. Построить модель, которая могла бы спрогнозировать вероятность отчисления с эконома конкретного человека, а после спрогнозировать такие вероятности для всех текущих первокурсников. Сессия близко, и они должны понимать кому из них особенно стоит засесть за ботанье. Обычно строить подобные модели для прогнозирования успешности людей, довольно сложно. Более того, выборка, которая в конечном счёте окажется в наших руках, окажется небольшой. Жалкие 300 человек...  Тем не менее это нас никак не остановит. В макроэкономике обычно намного меньше наблюдений. Тем не менее это не останавливает их от строительства прогнозов...

## Какие у нас есть данные

Прежде чем окунуться с головой в моделирование и анализ, данные нужно собрать. У нас будет три источника данных:

###### Приказы с сайта академии и из групп студентов.

![ скриншот приказа ]( )

В таких приказах обычно указывается в каком году поступил студент, на каких основаниях и с каким баллом за ЕГЭ. Я собрал для исследования все такие приказы с 2012 по 2017 годы и перекинул все данные из них в красивые таблички.

![ скриншот таблички ]( )

В табличках оказались такие переменные как балл за ЕГЭ, проходной балл, год набора, являлся ли человек льготником, целевиком или олимпиадником, фамилия, имя. В конечном итоге в нашем распоряжении окажется $433$ человека.

###### Асессоры

К сожалению, приказы об отчислении не лежат в открытом доступе. Поэтому когда на основе приказов по каждому курсу была подготовлена соответствующая табличка, в дело вступили асессоры. Асессоры это специально обученые люди, которые занимаются разметкой данных. С каждого курса я попытался выделить три независимые группы асессоров. Каждая группа попыталась вспомнить а когда же отчислили с факультета каждого конкретного человека, как часто он ходил на пары, как часто ходил на тусы и пытался ли он после отчисления вернуться. Таким образом появилось несколько новых переменных.

![картинка с асессорскими переменными]( )

Переменная kurs ставилась асессорами по шкале от 1 до текущего курса, на котором учится человек. Цифра означает то, с какого курса отчислили человека. Так, например, если человек должен был бы сейчас учиться на третьем курсе, но его выгнали со второго, в графе курс для него будет стоять цифра 2. Если человек уже успешно выпустился с иканама, ему ставилась цифра 5.

Переменная Leto-zima соответствует той сессии, которую не перенёс человек. Переменная Akadem принимает значение 1, если человек пытался после отчисления восстановиться или взял академ.

Переменные hodit_para измеряет по шкале от 0 до 5 интенсивность, с которой человек в среднем за период своего обучения ходил на пары. Переменная hodit_tusa измеряет по шкале от 0 до 5 интенсивность, с которой, по мнению асессоров, человек ходил тусить (не только бухать, а в принципе тусить) со своим курсом. Эти две переменные являются экспериментальными. Они довольно субьективны.

Толпа бывает мудрой. Фрэнсис Гальтон в 1906 году оказался в Портсмуте, в Западной Англии. Там в рамках  Выставки животноводства и птицеводства на рынке проводился конкрус, в ходе которого крестьяне должны были угадать сколько весит бык. Их собралось около 800 человек. Бык весил 1198 фунтов. Ни один крестьянин не угадал точный вес быка. Среднее значение их предсказаний оказалось равным 1197 фунтам. Усреднённое мнение толпы оказалось близким к истине. С тех пор такой эффект называют мудростью толпы.

Другой классический пример подобного явления датируется маем 1968 года, когда появилось сообщение, что атомная подводная лодка Военно-морских сил США «Скорпион», вооружённая двумя торпедами с ядерными боеголовками и с 99 членами экипажа на борту, пропала без вести. Известно было лишь то, что до своего исчезновения подлодка находилась в Атлантическом океане где-то в пределах круга диаметром в 32 километра. Пять месяцев интенсивных поисков не привели ни к какому результату. Наконец работавший на BMC учёный Джон Крэйвен предложил группе экспертов-подводников и спасателей угадат местонахождение подлодки, а затем усреднил все собранные данные. В итоге подводная лодка была найдена всего в 220 метрах от коллективно предсказанной точки. К сожалению, к тому времени вся команда уже давно погибла (подробнее гугли the untold story of american submarine espionage).

Эту идею уменьшения ошибки путём некоторого усреднения разных мнений успешно применяют сегодня в машинном обучении. Чаще всего различные алгоритмы высказывают свои мнения, а после они усредняются. Итоговый прогноз получается более точным. В нашем случае, своим мнения выскажут асессоры. После мы усредним их мнения, взяв медиану. В конечном счёте это даст нам близкие к истине значения курсов, с которых были отчислены люди, а также более-менее адекватные значения показателей hodit_para и hodit_tusa. Тем не менее, не стоит забывать, что три это ещё не толпа.

###### Вконтакте

Главным источником знаний в нашем исследовании будет социальная сеть, в которой каждый человек оставляет свой уникальный след. Прежде, чем скачать данные из соц сети, надо найти все акаунты, которые соответствуют иканомчанам. Для этого мы собираем все группы иканама разных лет:
* иканам  https://vk.com/ikanam
* набор 2017  https://vk.com/economist2017
* набор 2016  https://vk.com/ikanamchik
* набор 2015  https://vk.com/efka2015
* набор 2014  https://vk.com/ekfa14
* набор 2013  https://vk.com/club102346076
* набор 2012  https://vk.com/economy.rane
* набор 2011  https://vk.com/club29313387
* набор 2010  https://vk.com/club19743751

После мы напишем код, который будет сверять людей из групп с людьми из приказов и при наличии соотвествия будет проставлять uid (уид) на страницу человека. После мы попросим асессоров дозаполнять тех людей, которые не были найдены и проверить ссылки на тех, кого наш алгоритм нашёл. В итоге в выборке окажется 706 человек. По полученному списку из 706 уидов мы и будем собирать из контакта данные.


## О том, почему вконтакте самая крутая соц сеть.

Многие сайты и сервисы хотят упростить жизнь программистам и предоставляют им в пользование уже готорвые куски кода, к которым можно просто обратиться и получить нужную инфомрацию о сервисе. Такие куски кода называются API (Application programming interface). Обычно, чтобы подключиться к API, надо создать в соц-сетке своё приложение (как счастливый фермер, но не совсем). Это занимает около 5 минут и даёт тебе ключ доступа.

Вконтакте не является исключением. API Вконтакте это рай для дата-сайентиста. Можно скачать огромное количество всего без серьёзных ограничений, а после заисследовать до потери пульса.

Другие социальные сети ведут себя по отношению к мирным собирателям данных, вроде меня, довольно агрессивно.  Например, Facebook по своему API отдаст вам информацию только о тех людях, которые установили ваше приложение, API Одноклассников потребует написать им письмо с объяснением того, зачем это вам понадобились их данные, а API Вконтакте отдаст вам всё практически задаром. Единственное, что он попросит — это не обращаться к сайту очень часто, и это очень круто.

Тем не менее, недавно Вконтакте провёл серьёзную реформу своей музыкальной части и монетизировал её. Это привело к изъятию куска API, связанного с музыкой из открытого доступа. Более того, существует опасность, что социальная сеть продолжит резать бесплатный функционал своего API после разных скандалов, связанных с банками, нагло собирающими информацию о пользователях для своих скоринговых моделей.

Надо успеть накачать данных. О том как это можно сделать, читайте вот в этом туториале.

![ ссылка на туториал ]( )

Сам по себе код со скачкой данных и подробными комментариями к нему, находится вот тут

![ код со скачкой данных]( )

Здесь же я просто расскажу о тех данных, которые скачал чуть более подробно.

### Данные о профиле

Сначала будем сохранять все данные в черновом виде. В том числе и бесполезные переменные. Потом мы от них избавимся.  

По каждому иканамовцу скачаем всё, что только можно скачать. Дату рождения, город, пол, состоит ли он в отношениях.

![ ](images/1.scr_table_1.jpg)

После я добавил сюда информацию о том какую именно активность предпочитает человек, что написано у него в профиле, есть ли ссылки на какие-то другие сервисы вроде facebook и так далее.

![ ](images/1.scr_table_2.png)

Уже на этом этапе я понял, что с парнем по имени Юра Сарафанюк что-то не так. Скорее всего, он подписан на мусорные паблики вроде MDK, но о пабликах речь пойдёт ниже.

Конечно же не обошлось без скачки информации о том как человек относится к алкоголю (Юрий относится к алкоголю крайне положительно), информации о количестве фоток, подарков, друзей, подписчиков, религии, политических предпочтений и многого другого.  

![ ](images/1 scr_table_3.png)

Конечно же, оказалось, что многие люди из ряда вон плохо заполняют свои профили. Кто-то не указывает свою религию, кто-то свои любимые фильмы. Все эти переменные нам в конечном итоге придётся выбросить. Погоды по ним не сделаешь. Все данные по профилям лежат в папке data. Табличка носит гордое имя `vk_data_profile_дата_скачки.csv`.

### Аватарки

После я скачал информацию о аватарках. Я скачал ссылку на фоточку, а также информацию о том как именно человек обрезал её когда устанавливал на аватарку. Эта информация лежит в файле `vk_data_photos_дата_скачки.pickle`. Pickle это не огурчик, это специальный формат для хранения данных. :)

### Паблики

После я скачал инфу о том кто и на какие паблики подписан. Она лежит в файле `vk_data_group_дата_скачки.pickle` в виде словарика

```
{uid : вектор из пабликов, на которые подписан uid}
```
С авками и пабликами мы будем работать отдельно. Для пабликов мы попытаемся применить такую страшную штуку, как тематическое моделирование. Оно поможет нам разделить паблики на несколько разных групп по их тематикам. Это будет политика, мемы и многое другое. Фоточки мы загоним в нейросети. Одна из сеток попытается предсказать отчисление человека по его аватару.

### Стены юзеров и фотки из альбомов

Стенки и фотки это огромное море контента. Интересно будет узнать что именно репостят люди, что выкладывают и как лайкают...

Стенки скачаем в двух видах. В грязном виде стенки будут выглядеть вот так:  

![ ](images/1 scr_table_wall.png)

Дата поста, контент этого поста, чей это пост, репостнут ли он, с какого устройства был сделан этот пост (iphone, android и тп). Можно будет оценить примерную долю яблочников на экономе, а также найти тех, кто совершает против человечества преступления (пользуется windows phone).

На основе таких грязных стен мы попробуем выделить по каждому человеку интересные переменные. Например, эмодзи-след человека, среднее количество слов в посте, максимальное количествос слов в посте, в скольки постах встречаются фотки, музыка и так далее.


Грязные стены в виде словариков лежат в файле `vk_dirty_wall_дата_скачки.pickle`. Все посты всего иканама в виде таблички лежат в файле `vk_dirty_wall_data_дата_скачки.csv`. Стены переработанные в итоговые, интересующие нас переменные, лежат в табличке `vk_wall_data_дата_скачки.csv`

С фотками мы поступим аналогично. На выход пойдёт три таблички. `vk_dirty_photo_дата_скачки.pickle`, `vk_dirty_photo_data_дата_скачки.csv`, `vk_photo_data_дата_скачки.csv`.


### Лайки

Осталось скачать самое главное и самое ресурсоёмкое, лайки. Для начала мы скачаем все лайки, которые стоят под фотками и все лайки, которые стоят под постами для каждого юзера. К счастью, это тоже можно сделать. Мы скачаем все лайки под постами на стене в файл `vk_total_wal_likes_дата_скачки.pickle` в виде `{дата поста : uid пользователей, который лайкнули пост}`. Лайки под фотками сохраним в файл `'vk_total_photo_likes_дата_cкачки.pikcle` в аналогичном формате.

![ ](images/1 scr wall_like.png)

### Скачаем чуть позже

Чуть позже, когда до этого дойдут руки, мы скачаем из тех пабликов, на которые подписан иканам по 100 самых свежих постов, а также лайки под каждым из этих постов. Ну а сейчас пришло время первых исследований и визуализаций.


## Граф иканама

Граф друзей. Это звучит довольно круто. Граф иканама. Это звучит ещё круче. В нашем распоряжении есть