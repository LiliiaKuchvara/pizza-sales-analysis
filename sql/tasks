-- КРОК 1
-- Кількість рядків у кожній таблиці

SELECT COUNT(*) AS orders_count
FROM orders;

SELECT COUNT(*) AS order_details_count
FROM order_details;

SELECT COUNT(*) AS pizzas_count
FROM pizzas;

SELECT COUNT(*) AS pizza_types_count
FROM pizza_types; 
--Висновок: структура даних відповідає опису датасету.
--Найбільша таблиця — order_details, яка містить окремі позиції замовлень.
 
-- Перевірка пропусків у orders

SELECT
    SUM(CASE WHEN order_id IS NULL THEN 1 ELSE 0 END) AS missing_order_id,
    SUM(CASE WHEN date IS NULL THEN 1 ELSE 0 END) AS missing_date,
    SUM(CASE WHEN time IS NULL THEN 1 ELSE 0 END) AS missing_time
FROM orders;
--Висновок: у таблиці orders пропущені значення відсутні. Дані про номер замовлення, 
--дату та час заповнені повністю.

--Перевіримо інші 3 таблиці
-- order_details
SELECT
    SUM(CASE WHEN order_details_id IS NULL THEN 1 ELSE 0 END) AS missing_order_details_id,
    SUM(CASE WHEN order_id IS NULL THEN 1 ELSE 0 END) AS missing_order_id,
    SUM(CASE WHEN pizza_id IS NULL THEN 1 ELSE 0 END) AS missing_pizza_id,
    SUM(CASE WHEN quantity IS NULL THEN 1 ELSE 0 END) AS missing_quantity
FROM order_details;
-- pizzas
SELECT
    SUM(CASE WHEN pizza_id IS NULL THEN 1 ELSE 0 END) AS missing_pizza_id,
    SUM(CASE WHEN pizza_type_id IS NULL THEN 1 ELSE 0 END) AS missing_pizza_type_id,
    SUM(CASE WHEN size IS NULL THEN 1 ELSE 0 END) AS missing_size,
    SUM(CASE WHEN price IS NULL THEN 1 ELSE 0 END) AS missing_price
FROM pizzas; 
-- pizza_types
SELECT
    SUM(CASE WHEN pizza_type_id IS NULL THEN 1 ELSE 0 END) AS missing_pizza_type_id,
    SUM(CASE WHEN name IS NULL THEN 1 ELSE 0 END) AS missing_name,
    SUM(CASE WHEN category IS NULL THEN 1 ELSE 0 END) AS missing_category,
    SUM(CASE WHEN ingredients IS NULL THEN 1 ELSE 0 END) AS missing_ingredients
FROM pizza_types; 
--Перевірка якості даних
--Було проведено перевірку на наявність пропущених значень (NULL) у всіх таблицях бази даних.
-- Пропуски не виявлені. Дані є повними та придатними для подальшого аналізу.

--Питання №1 — KPI за рік
SELECT
    ROUND(SUM(p.price * od.quantity), 2) AS revenue,
    COUNT(DISTINCT o.order_id) AS total_orders,
    SUM(od.quantity) AS pizzas_sold,

    ROUND(
        SUM(p.price * od.quantity)
        /
        COUNT(DISTINCT o.order_id),
        2
    ) AS average_order_value,

    ROUND(
        CAST(SUM(od.quantity) AS REAL)
        /
        COUNT(DISTINCT o.order_id),
        2
    ) AS average_pizzas_per_order

FROM orders o
JOIN order_details od
    ON o.order_id = od.order_id
JOIN pizzas p
    ON od.pizza_id = p.pizza_id; 
    
---- ПИТАННЯ 2
-- Виручка та замовлення по місяцях
SELECT
    strftime('%m', o.date) AS month,

    ROUND(
        SUM(od.quantity * p.price),
        2
    ) AS revenue,

    COUNT(DISTINCT o.order_id) AS orders_count

FROM orders o

JOIN order_details od
    ON o.order_id = od.order_id

JOIN pizzas p
    ON od.pizza_id = p.pizza_id

GROUP BY month

ORDER BY month;

--Зміна до попереднього місяця (%) 
WITH monthly_sales AS (
    SELECT
        strftime('%m', o.date) AS month,
        ROUND(SUM(od.quantity * p.price), 2) AS revenue
    FROM orders o
    JOIN order_details od
        ON o.order_id = od.order_id
    JOIN pizzas p
        ON od.pizza_id = p.pizza_id
    GROUP BY month
)

SELECT
    month,
    revenue,

    LAG(revenue) OVER (
        ORDER BY month
    ) AS previous_month_revenue,

    ROUND(
        (
            revenue - LAG(revenue) OVER (ORDER BY month)
        ) * 100.0
        /
        LAG(revenue) OVER (ORDER BY month),
        2
    ) AS growth_percent

FROM monthly_sales;

--3.1 Замовлення за днями тижня
SELECT
    CASE strftime('%w', date)
        WHEN '0' THEN 'Sunday'
        WHEN '1' THEN 'Monday'
        WHEN '2' THEN 'Tuesday'
        WHEN '3' THEN 'Wednesday'
        WHEN '4' THEN 'Thursday'
        WHEN '5' THEN 'Friday'
        WHEN '6' THEN 'Saturday'
    END AS weekday,

    COUNT(DISTINCT order_id) AS orders_count

FROM orders
GROUP BY strftime('%w', date)
ORDER BY orders_count DESC;
--висновок
--Найбільше замовлень надходить у п'ятницю (3538).
--Друге місце займає четвер (3239).
--Найменше замовлень надходить у неділю (2624).
--Кінець робочого тижня (четвер–п'ятниця) є найактивнішим періодом для продажів.

--3.2 Замовлення за годинами
SELECT
    strftime('%H', time) AS hour,
    COUNT(DISTINCT order_id) AS orders_count
FROM orders
GROUP BY hour
ORDER BY orders_count DESC;

--Висновок
--Основний пік продажів припадає на обідній час (12:00–13:00). 
--Другий виражений пік спостерігається увечері (17:00–19:00). 
--Найменше замовлень надходить вранці до 11:00 та пізно ввечері після 22:00. 
--Отже, найбільший попит формується під час обіду та вечері, що характерно для ресторанного бізнесу.

--3.3 Середня кількість замовлень за один понеділок, вівторок і т.д.
SELECT
    CASE strftime('%w', date)
        WHEN '0' THEN 'Sunday'
        WHEN '1' THEN 'Monday'
        WHEN '2' THEN 'Tuesday'
        WHEN '3' THEN 'Wednesday'
        WHEN '4' THEN 'Thursday'
        WHEN '5' THEN 'Friday'
        WHEN '6' THEN 'Saturday'
    END AS weekday,

    COUNT(DISTINCT order_id) AS total_orders,
    COUNT(DISTINCT date) AS days_count,

    ROUND(
        CAST(COUNT(DISTINCT order_id) AS REAL)
        / COUNT(DISTINCT date),
        2
    ) AS avg_orders_per_day

FROM orders
GROUP BY strftime('%w', date)
ORDER BY avg_orders_per_day DESC; 
--Висновок 
--Найбільше замовлень у середньому надходить у п'ятницю — 70.76 замовлень за день. 
--Також високий попит спостерігається у четвер (62.29) та суботу (60.73). 
--Найменше замовлень припадає на неділю — 50.46 замовлень за день. 
--Різниця між найактивнішим і найспокійнішим днем становить близько 20 замовлень на день, 
--що свідчить про помітне зростання попиту наприкінці робочого тижня.

--4.1 ТОП-5 піц за виручкою
SELECT
    pt.name AS pizza_name,
    ROUND(SUM(od.quantity * p.price), 2) AS revenue
FROM order_details od
JOIN pizzas p
    ON od.pizza_id = p.pizza_id
JOIN pizza_types pt
    ON p.pizza_type_id = pt.pizza_type_id
GROUP BY pt.name
ORDER BY revenue DESC
LIMIT 5; 
--Найбільшу виручку генерує The Thai Chicken Pizza ($43,434), за нею йдуть The Barbecue Chicken Pizza
-- та The California Chicken Pizza. У топ-5 переважають курячі піци, що свідчить про високу популярність 
-- цієї категорії серед клієнтів. Найкраща піца за виручкою приносить приблизно на 27% більше доходу, 
-- ніж п'ята позиція рейтингу.

--4.2 Останні 5 піц за виручкою
SELECT
    pt.name AS pizza_name,
    ROUND(SUM(od.quantity * p.price), 2) AS revenue
FROM order_details od
JOIN pizzas p
    ON od.pizza_id = p.pizza_id
JOIN pizza_types pt
    ON p.pizza_type_id = pt.pizza_type_id
GROUP BY pt.name
ORDER BY revenue ASC
LIMIT 5;
--Висновок 
--Найменшу виручку приносить The Brie Carre Pizza ($11,589). Також до групи аутсайдерів входять 
--The Green Garden Pizza, The Spinach Supreme Pizza, The Mediterranean Pizza та The Spinach Pesto Pizza. 
--Виручка цих позицій майже в 3–4 рази нижча, ніж у лідерів продажів, що може свідчити про нижчий попит або 
--менш привабливий асортимент для клієнтів.

--4.3 ТОП-5 піц за кількістю проданих штук
SELECT
    pt.name AS pizza_name,
    SUM(od.quantity) AS pizzas_sold
FROM order_details od
JOIN pizzas p
    ON od.pizza_id = p.pizza_id
JOIN pizza_types pt
    ON p.pizza_type_id = pt.pizza_type_id
GROUP BY pt.name
ORDER BY pizzas_sold DESC
LIMIT 5;

--Висновок
--Найбільш продаваною є The Classic Deluxe Pizza (2453 шт.). Також до п'ятірки лідерів входять 
--The Barbecue Chicken Pizza, The Hawaiian Pizza, The Pepperoni Pizza та The Thai Chicken Pizza. 
--Частина лідерів за кількістю продажів збігається з лідерами за виручкою, проте деякі піци (наприклад, Hawaiian 
--та Pepperoni) продаються часто, але не входять до топу за доходом через нижчу середню вартість.

--4.4 Останні 5 піц за кількістю проданих штук
SELECT
    pt.name AS pizza_name,
    SUM(od.quantity) AS pizzas_sold
FROM order_details od
JOIN pizzas p
    ON od.pizza_id = p.pizza_id
JOIN pizza_types pt
    ON p.pizza_type_id = pt.pizza_type_id
GROUP BY pt.name
ORDER BY pizzas_sold ASC
LIMIT 5;

--Висновок: Найменш популярною за кількістю проданих штук є The Brie Carre Pizza (490 шт.), тоді як
-- The Mediterranean Pizza, The Calabrese Pizza, The Spinach Supreme Pizza та The Soppressata Pizza також 
-- входять до групи аутсайдерів із продажами менше 1000 штук. Це свідчить про значно нижчий попит на ці позиції 
-- порівняно з лідерами, які продаються більш ніж удвічі частіше.
--Також помітно, що частина цих піц (The Brie Carre Pizza, The Mediterranean Pizza, The Spinach Supreme Pizza)
-- одночасно входить до числа найслабших і за виручкою, і за кількістю продажів, що підтверджує їхню низьку 
-- комерційну ефективність.

--5.1 Частка виручки кожної категорії
SELECT
    pt.category,
    ROUND(SUM(od.quantity * p.price), 2) AS revenue,
    ROUND(
        SUM(od.quantity * p.price) * 100.0 /
        SUM(SUM(od.quantity * p.price)) OVER (),
        2
    ) AS revenue_share_percent
FROM order_details od
JOIN pizzas p
    ON od.pizza_id = p.pizza_id
JOIN pizza_types pt
    ON p.pizza_type_id = pt.pizza_type_id
GROUP BY pt.category
ORDER BY revenue DESC;
--Висновок 
--Категорія Classic генерує найбільшу частку виручки — 26,91% ($220 053,10), що робить її основним джерелом 
--доходу компанії. Далі йдуть Supreme (25,46%), Chicken (23,96%) та Veggie (23,68%).
--Різниця між категоріями є відносно невеликою (близько 3 відсоткових пунктів між лідером і останнім місцем), 
--тому попит на різні категорії піци розподілений досить рівномірно. Найменший внесок у виручку серед показаних
-- категорій має Veggie, однак її частка все одно перевищує 23%, що свідчить про стабільний інтерес клієнтів до 
-- всіх основних категорій меню.

--5.2 Частка виручки кожного розміру
SELECT
    p.size,
    ROUND(SUM(od.quantity * p.price), 2) AS revenue,
    ROUND(
        SUM(od.quantity * p.price) * 100.0 /
        SUM(SUM(od.quantity * p.price)) OVER (),
        2
    ) AS revenue_share_percent
FROM order_details od
JOIN pizzas p
    ON od.pizza_id = p.pizza_id
GROUP BY p.size
ORDER BY revenue DESC;
--Висновок
--Найбільшу частку виручки забезпечують піци розміру L — 45,89% від загального доходу ($375 318,70), 
--що майже дорівнює половині всієї виручки. Друге місце займає розмір M (30,49%), а третє — S (21,77%).
--Водночас великі формати XL та XXL мають незначний внесок у дохід — лише 1,72% та 0,12% відповідно. 
--Отже, основний попит клієнтів зосереджений на стандартних розмірах L, M та S, причому розмір L є беззаперечним
-- лідером за обсягом отриманої виручки.

--5.3 Які розміри продаються найкраще?
SELECT
    p.size,
    SUM(od.quantity) AS pizzas_sold
FROM order_details od
JOIN pizzas p
    ON od.pizza_id = p.pizza_id
GROUP BY p.size
ORDER BY pizzas_sold DESC;
--Висновок 
--Найпопулярнішим розміром є L, для якого було продано 18 956 піц, що робить його беззаперечним лідером за 
--обсягом продажів. Далі за популярністю йдуть розміри M (15 635 шт.) та S (14 403 шт.), які також користуються 
--стабільним попитом серед клієнтів.
--Водночас великі розміри XL (552 шт.) та особливо XXL (28 шт.) продаються значно рідше. Отже, клієнти 
--переважно обирають стандартні розміри L, M та S, причому розмір L є лідером як за кількістю продажів, 
--так і за отриманою виручкою.

--6.1 Продажі, виручка та частка виручки кожної піци 
SELECT
    pt.name AS pizza_name,
    SUM(od.quantity) AS pizzas_sold,
    ROUND(SUM(od.quantity * p.price), 2) AS revenue,
    ROUND(
        SUM(od.quantity * p.price) * 100.0 /
        SUM(SUM(od.quantity * p.price)) OVER (),
        2
    ) AS revenue_share_percent
FROM order_details od
JOIN pizzas p
    ON od.pizza_id = p.pizza_id
JOIN pizza_types pt
    ON p.pizza_type_id = pt.pizza_type_id
GROUP BY pt.name
ORDER BY revenue ASC; 
--Висновок
--Аналіз показав, що найменш ефективними позиціями меню є The Brie Carre Pizza, The Green Garden Pizza, 
--The Spinach Supreme Pizza, The Mediterranean Pizza та The Spinach Pesto Pizza. Вони демонструють одночасно 
--низькі обсяги продажів (від 490 до 997 штук) та найменшу частку виручки (1,42–1,91%).

--6.2 Накопичувальна частка виручки (SUM() OVER)
SELECT
    pt.name AS pizza_name,
    SUM(od.quantity) AS pizzas_sold,
    ROUND(SUM(od.quantity * p.price), 2) AS revenue,

    ROUND(
        SUM(od.quantity * p.price) * 100.0 /
        SUM(SUM(od.quantity * p.price)) OVER (),
        2
    ) AS revenue_share_percent,

    ROUND(
        SUM(
            SUM(od.quantity * p.price)
        ) OVER (
            ORDER BY SUM(od.quantity * p.price)
        ) * 100.0
        /
        SUM(SUM(od.quantity * p.price)) OVER (),
        2
    ) AS cumulative_share_percent

FROM order_details od
JOIN pizzas p
    ON od.pizza_id = p.pizza_id
JOIN pizza_types pt
    ON p.pizza_type_id = pt.pizza_type_id
GROUP BY pt.name
ORDER BY revenue ASC;
--Висновок
--Аналіз накопичувальної частки виручки показує, що найменший внесок у дохід роблять The Brie Carre Pizza,
-- The Green Garden Pizza, The Spinach Supreme Pizza, The Mediterranean Pizza та The Spinach Pesto Pizza. 
-- Разом ці п’ять позицій забезпечують лише 8,78% загальної виручки, хоча займають майже шосту частину асортименту.
--Якщо розглядати оптимізацію меню, саме ці піци є першими кандидатами на вилучення, оскільки мають одночасно низькі
-- продажі та низьку частку виручки. Їх вилучення вплине на дохід незначно (менше 9%), але може спростити меню та 
-- зменшити витрати на закупівлю інгредієнтів і підтримку асортименту.

--6.3 Обґрунтування кандидатів на вилучення з меню 

WITH pizza_stats AS (
    SELECT
        pt.name AS pizza_name,
        SUM(od.quantity) AS pizzas_sold,
        ROUND(SUM(od.quantity * p.price), 2) AS revenue,
        ROUND(
            SUM(od.quantity * p.price) * 100.0 /
            SUM(SUM(od.quantity * p.price)) OVER (),
            2
        ) AS revenue_share_percent
    FROM order_details od
    JOIN pizzas p
        ON od.pizza_id = p.pizza_id
    JOIN pizza_types pt
        ON p.pizza_type_id = pt.pizza_type_id
    GROUP BY pt.name
)
SELECT
    pizza_name,
    pizzas_sold,
    revenue,
    revenue_share_percent
FROM pizza_stats
ORDER BY revenue_share_percent ASC
LIMIT 5;
--Висновок
--За результатами аналізу найменшу частку виручки та найнижчі обсяги продажів мають The Brie Carre Pizza, 
--The Green Garden Pizza, The Spinach Supreme Pizza, The Mediterranean Pizza та The Spinach Pesto Pizza. 
--Кожна з цих позицій формує менше 2% загальної виручки, а їхній сукупний внесок становить лише 8,79%.

--7.1 Частка великих замовлень (4+ піц) та їхня виручка
WITH order_stats AS (
    SELECT
        o.order_id,
        SUM(od.quantity) AS pizzas_count,
        SUM(od.quantity * p.price) AS revenue
    FROM orders o
    JOIN order_details od
        ON o.order_id = od.order_id
    JOIN pizzas p
        ON od.pizza_id = p.pizza_id
    GROUP BY o.order_id
)
SELECT
    COUNT(*) AS total_orders,
    SUM(CASE WHEN pizzas_count >= 4 THEN 1 ELSE 0 END) AS large_orders,
    ROUND(
        SUM(CASE WHEN pizzas_count >= 4 THEN 1 ELSE 0 END) * 100.0 /
        COUNT(*),
        2
    ) AS large_orders_percent,
    ROUND(
        SUM(CASE WHEN pizzas_count >= 4 THEN revenue ELSE 0 END),
        2
    ) AS large_orders_revenue,
    ROUND(
        SUM(CASE WHEN pizzas_count >= 4 THEN revenue ELSE 0 END) * 100.0 /
        SUM(revenue),
        2
    ) AS revenue_share_percent
FROM order_stats;
--Висновок
--Аналіз показав, що лише 18,17% усіх замовлень містять 4 і більше піц, проте вони формують 39,44% загальної 
--виручки закладу. Це означає, що великі замовлення приносять непропорційно високий дохід порівняно зі своєю 
--кількістю та є важливим джерелом виручки.
--Отримані результати свідчать про доцільність розробки спеціальних пропозицій для компаній та великих груп клієнтів.
-- Знижки на великі замовлення, корпоративні набори або бонуси за замовлення кількох піц можуть стимулювати збільшення
--  кількості таких покупок і позитивно вплинути на загальний дохід закладу.

--8.1 Визначити найкращий час для акції
SELECT
    strftime('%w', date) AS weekday,
    strftime('%H', time) AS hour,
    ROUND(SUM(od.quantity * p.price), 2) AS revenue
FROM orders o
JOIN order_details od
    ON o.order_id = od.order_id
JOIN pizzas p
    ON od.pizza_id = p.pizza_id
GROUP BY weekday, hour
ORDER BY revenue; 
--Висновок:
--Аналіз виручки за днями тижня та годинами показав, що найбільший дохід генерується в обідній час, особливо
-- о 12:00–13:00. Найкращим часовим вікном для запуску акції є саме цей період, оскільки він характеризується 
-- найвищим попитом і дозволяє отримати максимальний ефект навіть від невеликого збільшення продажів. 
-- Це робить обідню акцію найбільш перспективним варіантом для зростання виручки.

--8.2 Оцінка потенціалу акції (+10% до продажів)
WITH revenue_by_slot AS (
    SELECT
        CAST(strftime('%w', o.date) AS INTEGER) AS weekday,
        CAST(substr(o.time, 1, 2) AS INTEGER) AS hour,
        SUM(od.quantity * p.price) AS revenue
    FROM orders o
    JOIN order_details od
        ON o.order_id = od.order_id
    JOIN pizzas p
        ON od.pizza_id = p.pizza_id
    GROUP BY weekday, hour
)

SELECT
    weekday,
    hour,
    ROUND(revenue, 2) AS current_revenue,
    ROUND(revenue * 1.10, 2) AS revenue_after_growth,
    ROUND(revenue * 0.10, 2) AS additional_revenue
FROM revenue_by_slot
ORDER BY revenue DESC
LIMIT 10; 
--Висновок:
--Найкращим часовим вікном для запуску акції є 12:00–13:00 у день з кодом weekday = 4, оскільки в цей період
-- зафіксовано найбільшу поточну виручку — 19 042,20.
--Якщо завдяки акції продажі зростуть на 10%, то:
--додаткова виручка становитиме 1 904,22;
--загальна виручка збільшиться до 20 946,42.
--Отже, доцільно запустити обідню акцію (12:00–13:00), наприклад комбо-набір або знижку на другу піцу.
-- Навіть помірне зростання попиту на 10% забезпечить майже 1,9 тис. додаткової виручки в найприбутковішому 
-- часовому слоті.

--8.3 Оцінити потенціал (+10%)
WITH revenue_by_slot AS (
    SELECT
        CAST(strftime('%w', o.date) AS INTEGER) AS weekday,
        CAST(substr(o.time, 1, 2) AS INTEGER) AS hour,
        SUM(od.quantity * p.price) AS revenue
    FROM orders o
    JOIN order_details od
        ON o.order_id = od.order_id
    JOIN pizzas p
        ON od.pizza_id = p.pizza_id
    GROUP BY weekday, hour
),

best_slot AS (
    SELECT *
    FROM revenue_by_slot
    ORDER BY revenue DESC
    LIMIT 1
)

SELECT
    weekday,
    hour,
    ROUND(revenue, 2) AS current_revenue,
    'Lunch Combo 10% Off' AS promotion,
    ROUND(revenue * 0.10, 2) AS expected_extra_revenue,
    ROUND(revenue * 1.10, 2) AS projected_revenue
FROM best_slot;
--Висновок
--Найбільший потенціал для проведення акції має п'ятниця о 12:00, коли поточна виручка становить 19 042,20. 
--Запуск акції «Lunch Combo 10% Off» у цей часовий проміжок може збільшити продажі на 10%, що принесе додатково
-- 1 904,22 виручки та підвищить загальну виручку до 20 946,42. Це робить даний часовий слот найбільш 
-- перспективним для маркетингової активності.


-- ВИБІРКА sales_lines
SELECT
    o.order_id AS order_id,
    o.date AS order_date,
    o.time AS order_time,
    CAST(strftime('%m', o.date) AS INTEGER) AS month,
    CASE strftime('%w', o.date)
        WHEN '0' THEN 'Sunday'
        WHEN '1' THEN 'Monday'
        WHEN '2' THEN 'Tuesday'
        WHEN '3' THEN 'Wednesday'
        WHEN '4' THEN 'Thursday'
        WHEN '5' THEN 'Friday'
        WHEN '6' THEN 'Saturday'
    END AS weekday,
    CAST(strftime('%H', o.time) AS INTEGER) AS hour,
    pt.name AS pizza_name,
    pt.category,
    p.size,
    p.price AS unit_price,
    od.quantity,
    od.quantity * p.price AS revenue
FROM orders o
JOIN order_details od ON o.order_id = od.order_id
JOIN pizzas p ON od.pizza_id = p.pizza_id
JOIN pizza_types pt ON p.pizza_type_id = pt.pizza_type_id;
