-- Гипотеза: увеличивает ли длительность матча вероятность покупки игроками рапиры(133 в item_ids)
-- Сразу стоит уточнить, что ячейки для айтемов представлены в бд только их конечным состоянием (т.е. какие айтемы
-- были у игроков под конец матча), но, тем не менее, мы можем все равно попробовать проследить эту вероятность,
-- так как, рапиру нельзя продать - она покупается навегда, выпадает при смерти, и подбирается кем-нибудь.
-- Единственная ситуация, когда мы ее можем пропустить - это когда игроки, убившие покупателя рапиры, отнесли рапиру
-- на базу. Однако, это довольно редкая ситуация, по карйней мере, это не должно сильно повлиять на результат.

-- Давайте для начала посмотрим, сколько в среднем золота тратят игроки и среднюю продолжительность матча(за все матчи)
SELECT round(avg(p.gold_spent), 2) as gold_spent,
       round(avg(m.duration) / 60 , 2) as duration
FROM players p
join match m on p.match_id = m.match_id
;

-- Получаем: в среднем тратится золота -  14157,61; в среднем матч длится 41.33 минуты.
-- Хорошо, теперь посмотрим на средние по всем матчам перцентили (25й и 75й) по золоту

with perc_gold_spent as(
    SELECT match_id,
           percentile_cont(0.25) within group (order by gold_spent) as gold_spent_percentile_25,
           percentile_cont(0.75) within group (order by gold_spent) as gold_spent_percentile_90
    FROM players
    GROUP BY match_id
    )
SELECT avg(gold_spent_percentile_25),
       avg(gold_spent_percentile_90)
FROM perc_gold_spent
;

-- Получаем: 25й - 10690,6 голды; 75й - 16831 голды. Вообще, дабы в какой-то мере уменьшить первый запрос, можно было бы
-- здесь найти 50й перцентиль(который равен 14157,61), но да ладно

-- Выведем матчи, в которых была куплена рапира, их длительность, потраченное золото и среднюю длительность среди таких матчей. Рядом
-- также добавлю среднюю длительность всех матчей в принципе и среднее кол-во потраченного золота всех матчей в принципе.
with matches_with_rapiers  as (
    SELECT p.match_id,
           p.item_0,
           p.item_1,
           p.item_2,
           p.item_3,
           p.item_4,
           p.item_5,
           avg(m.duration) over() as avg_duration,
           avg(p.gold_spent) over() as avg_gp_rapier,
           m.duration,
           p.gold_spent

    FROM players p
             join match m on m.match_id = p.match_id
    WHERE item_0 = 133
       or item_1 = 133
       or item_2 = 133
       or item_3 = 133
       or item_4 = 133
       or item_5 = 133
),
     avg_duration_all_matches as(
         SELECT avg(duration) as avg_duration_all
         FROM match
),
     avg_gs_all as(
         SELECT avg(gold_spent) as avg_gs
         FROM players
     )
select match_id,
       duration,
       round(avg_duration, 2) as avg_duration_with_rapiers,
       round(avg_duration_all_matches.avg_duration_all, 2) as avg_duration_all,
       gold_spent,
       round(avg_gs_all.avg_gs, 2) as avg_gs,
       round(avg_gp_rapier, 2) as avg_gs_with_rapiers

FROM matches_with_rapiers,avg_duration_all_matches, avg_gs_all

-- Если запустить запрос, то из полученной таблицы заметно, что средняя длительность матчей, в которых была куплена рапира действительно
-- больше (на ~300 секунд), таким образом, продолжительность матча действительно увеличивает вероятность покупки игроками такого
-- чудесного предмета, как Divine Rapier. Стоит заметить, однако, что работает это не всегда. Но, тем не менее, это довольно часто
-- влияет, так как мало ситуаций, когда длительность матча ниже средней и игрок при этом купил рапиру.




