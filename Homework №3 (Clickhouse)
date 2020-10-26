-- Задание №1 Воронки
select v.tariff,
       uniqExact(v.idhash_view)  as views_count,
       uniqExact(o.idhash_order) as orders_count,
       uniqExact(o.da_dttm)      as driver_appointed,
       uniqExact(o.rfc_dttm)     as car_served,
       uniqExact(o.cc_dttm)      as client_sat,
       uniqExact(o.finish_dttm)  as cp_trip
from views v
         any
         left join orders o on v.idhash_order = o.idhash_order
group by v.tariff
;
-- теряем, безусловно, больше всего на шаге просмотров[views_count](что логично), далее, поделим столбцы: orders_count/driver_appointed = 1,27;
-- driver_appointed/car_served = 1,21). Таким образом, топ 2 по потере клиентов - этап заказа такси. Остальные столбцы не делил, потому что
-- там слишком очевидная небольшая разница.

-- Задание №2 По каждому клиенту выводим топ тарифов по убыванию в массиве и кол-во тарифов, которыми он пользуется
select idhash_client
     , groupArray(tariff) as tariffs
     , count(tariff)      as tariff_total
from (select idhash_client
           , tariff
           , uniqExact(idhash_order) as orders_
      from views
               left join orders o on views.idhash_order = o.idhash_order
      where 1 = 1
        and idhash_order != 0
        and status = 'CP'
      group by idhash_client, tariff
      order by orders_ desc
         )
group by idhash_client
;

-- Задание №3 Топ 10 гексагонов из которых уезжают с 7 до 10 утра и в которые едут с 18-00 до 20-00 в сумме по всем дням

select H3
     , count(H3) as orders_count

from (select multiIf((toHour(cc_dttm) >= 7) and (toHour(cc_dttm) <= 9),
                     geoToH3(longitude, latitude, 7) as H3_posadka, -- тут, наверное, уйдут заказы, сделанные ровно в 10
                     (toHour(finish_dttm) >= 18) and (toHour(finish_dttm) <= 19),
                     geoToH3(del_longitude, del_latitude, 7) as H3_finish, -- и ровно в 20 (не придумал, как реализовать)
                     null) as H3

      from orders
               any
               join views v on orders.idhash_order = v.idhash_order
         )
group by H3
order by orders_count DESC
limit 10
;
-- в конце подумал, а вот о каком топе идет речь? черт. эээ... в общем нужен такой запрос, который, как бы, спрашивает: этот сгруппированый гексагон до группировки
-- в большем количестве был представлен с часами от 7 до 9 или с часами от 18 до 19? и выводить что то типо 1 или 0.. я, если честно, не догадываюсь, Сергей.
-- реальные задачи очень сложны и интересны! :)

-- Задание №4 медиана и квантиль(95) времени поиска водителя (в минутах)
select round(medianIf(da_dttm - order_dttm, da_dttm >= 0) / 60, 2)              as median
     , round(quantileExactIf(0.95)(da_dttm - order_dttm, da_dttm >= 0) / 60, 2) as quantile_95
from orders
;

-- Задание №5 Кол-во клиентов, у которых в записи id_client есть подряд идущие цифры 5 и 7,  который совершал хотя бы 1 поездку на тарифе Бизнес
select uniqExact(idhash_client) as count_clients
from views
         any
         join orders o on views.idhash_order = o.idhash_order
where 1 = 1
  and cast(idhash_client as String) like '%57%'
  and status = 'CP'
  and tariff = 'Бизнес'
;
