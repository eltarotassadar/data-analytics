--1) количество матчей с FIRST BLOOD!!!! в интервале от 1 до 3 минут (не включая)
SELECT count(match_id) as FB_between_1_and_3_minutes
FROM match
WHERE (first_blood_time > 60 and first_blood_time < 180)
;

--2) Вывод id участников (!= 0), кооторые участвовали в матчах, где свет выиграл, и в матче больше лайков, чем дизлайков
SELECT distinct p.account_id as account_id
FROM players p
join match m on p.match_id = m.match_id
WHERE (p.account_id != 0)
  and (m.radiant_win = 'True')
  and (positive_votes > negative_votes)
;

--3) id игрока и средняя продолжительность его матчей
SELECT p.account_id as account_id,
       round(avg(m.duration / 60), 1) as duration_in_min

FROM players p
join match m on p.match_id = m.match_id
GROUP BY p.account_id
;

--4) для анонов получаем суммарное кол-во золота, кол-во исп. героев и среднюю продолжительность матчей
SELECT sum(p.gold_spent) as gold_spent,
       count(distinct p.hero_id) as heroes_count,
       round(avg(m.duration) / 60, 1) as avg_duration_in_min

FROM players p
join match m on p.match_id = m.match_id
WHERE (p.account_id = 0)
GROUP BY account_id
;

--5) для каждого героя выводим его кол-во сыгранных матчей, ср.кол-во убийств, минимум по смертям,
--   максимум по потраченному золоту, суммарно: матчи, в которых ставили лайк и матчи, в которых ставили дизлайк
SELECT h.localized_name as hero_name,
       count(distinct m.match_id) as match_count,
       round(avg(p.kills), 2) as avg_kills,
       min(p.deaths) as min_deaths,
       max(p.gold_spent) as max_gold_spent,
       sum(m.positive_votes) as sum_pos_votes,
       sum(m.negative_votes) as sum_dis_votes

FROM hero_names h
join players p on h.hero_id = p.hero_id
join match m on m.match_id = p.match_id
GROUP BY h.localized_name
;

--6) выводим матчи, в которых варды были куплены по прошествию 100 секунд
SELECT distinct match_id
FROM purchase_log
WHERE (item_id = 42)
and (time > 100)
;

--7) Собсна, задание, которое вызвало много споров, я в итоге понял так:
SELECT *
FROM purchase_log pl
join match m on pl.match_id = m.match_id --наверное, можно без джоина(не бейте палками)
LIMIT 20
;

--8) Для начала фантазии хватило на топ 10 по кол-ву убийств среди всех матчей (также выводятся герои,
--   на которых эти убийства были сделаны)
SELECT p.hero_id as hero_id,
       p.match_id as match_id,
       max(p.kills) as max_kills,
       hn.localized_name as hero_name

FROM players p
join hero_names hn on p.hero_id = hn.hero_id
GROUP BY p.match_id, p.hero_id, p.kills , hn.localized_name
ORDER BY p.kills desc
LIMIT 10
;

--9) Попробуем запрос такого вида: выведем среднее время получения игроками на пудже шестого уровня
SELECT au.level,
       round(avg(au.time) / 60, 2) as got_6_lvl

FROM ability_upgrades au
join ability_ids ai on au.ability = ai.ability_id
WHERE 1=1 and
      (ai.ability_id between 5074 and 5077) and -- диапазон 5074-5077 это скилы пуджа
      au.level = 6
GROUP BY au.level
;

--и тут я удивился. 15 минут?! Может, я что-то не так посчитал? попробуем для тинкера...

SELECT au.level,
       round(avg(au.time) / 60, 2) as got_6_lvl
FROM ability_upgrades au
join ability_ids ai on au.ability = ai.ability_id
WHERE 1=1 and
      (ai.ability_id between 5150 and 5153) and
      au.level = 6
GROUP BY au.level
;

-- ~13 минут. Я, конечно, давно не играл, но, кажется, это оч много. В общем, либо это данные собранные на таких супер-игроков,
-- либо я что-то не так делаю в запросе.

--10) Появилась идея по типу выяснить игроки за какую сторону чаще убивают рошана. Информация по рошану
--    в бд представлена лишь в виде полученного золота, причем золото ранжируется определенным образом... В общем,не успел реализовать

