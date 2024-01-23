### Отображение погоды и рекомендаций по управлению ТС

- МП каждые 5 минут отправляет в серверное ПО ПАК ИСС «Умный водитель» REST запросы с указанием координат ТС в формате WGS84 для получения актуальной на момент запроса информации о погодных условиях в районе нахождения ТС;
- В ответ МП получает актуальную на момент запроса информацию о [погодных условиях](#погодные-явления) в районе нахождения ТС и [рекомендации по управлению](#правила-формирования-рекомендаций-по-управлению-тс) ТС в соответствии с метеоданными (при наличии таковых);
  - Если ответ отсутствует - МП повторяет запрос в течение 5 минут с интервалами 1, 2, 4, 8, 16, … 28 сек;
  - При отсутствии ответа в течение 5 минут МП - выводится сообщение: «Погодный сервис временно недоступен, **_отсутствует ответ от сервера_**»;
  - Если метеоданные отсутствуют более 40 минут – выводится сообщение: «Погодный сервис временно недоступен, **_отсутствуют метеоданные более 40 минут_**»
  - Если отсутствует точка с данными о погоде в радиусе 20 км от местонахождения ТС - выводится сообщение: «Для данного места метеоданные отсутствуют»;
- Название населенного пункта указывается в формате: Населенный пункт, Вышестоящие административно-территориальные образования (до уровня страны, но не включая его)
  - При отсутствии названия населенного пункта отображается название места или вышестоящее административно-территориальное образование;
- Выводимая иконка погоды формируется на основании полученных данных с учетом параметров:

  - день / ночь – в соответствии с временем восхода и заката солнца;
  - облачность – от 0 до 4 ([таблица](#характеристики-облачности));
  - сила ветра – от 0 до 4 ([таблица](#характеристики-скорости-ветра));
  - интенсивность дождя – от 0 до 3, интенсивность снега – от 0 до 3, интенсивность смешанных осадков ([таблица](#характеристики-смешанных-осадков));
  - град – от 0 до 2 ([таблица](#характеристики-града));
  - вероятность грозы – да / нет.

    - Одновременное наличие ненулевой интенсивности у дождя и у снега – «Снег с дождем», интенсивность берется наибольшая из двух ([таблица](#характеристики-смешанных-осадков));
    - При отсутствии у МП иконки с указанным кодом - код игнорируется.

- Описание погоды формируется «склеиванием» характеристик ниже в указанном порядке:
  - облачность
  - интенсивность осадков (дождь, снег, град), кроме кодов 0;
  - вероятность грозы;
  - сила ветра (только для кодов 2 – 4: сильного, штормового, урагана);
  - дополнительные погодные явления.
- При _одновременном_ наличии и дождя и снега – на первом месте вид осадка с большей интенсивностью с указанием интенсивности. Интенсивность второго вида осадков игнорируется.  
  При _равной_ интенсивности осадков – на первом месте снег
- В случае отсутствия у МП словесной характеристики для кода - код игнорируется
- Значение температуры – в градусах Цельсия: значение округляется до целого по правилам математики. При положительном значении – выводится «+», при отрицательном – «-». Ноль – без знака;
- Дополнительная информация:
  - сила ветра (в м/с);
  - направление ветра;
  - влажность (в %);
  - атмосферное давление (в мм рт. ст.).

#### Рекомендации по управлению ТС

- Рекомендации передаются в виде массива кодов рекомендаций
- Отображаются в течение всего времени, пока действует указанное погодное явление
- Отображение новых рекомендаций сопровождается звуковым сигналом:
  - при добавлении новых рекомендаций при работе в активном и фоновом режимах;
  - при первой загрузке при старте приложения звуковой сигнал не подается;
  - при отмене одной или нескольких рекомендаций звуковой сигнал не подается.
- Информирование водителя в фоновом режиме выполняется с использованием Push-уведомлений.

##### Погодные явления

<table border="1">
    <thead>
        <th>
            Код
        </th>
        <th>
            Погодное явление
        </th>
    </thead>
        <tbody>
        <tr>
            <td>1</td>
            <td>Метель</td>
        </tr>
        <tr>
            <td>2</td>
            <td>Сильная метель</td>
        </tr>
        <tr>
            <td>3</td>
            <td>Гололед</td>
        </tr>
        <tr>
            <td>4</td>
            <td>Сильный гололед</td>
        </tr>
        <tr>
            <td>5</td>
            <td>Туман</td>
        </tr>
        <tr>
            <td>6</td>
            <td>Дым</td>
        </tr>
        <tr>
            <td>7</td>
            <td>Сильный туман</td>
        </tr>
        <tr>
            <td>8</td>
            <td>Пыльная буря</td>
        </tr>
        <tr>
            <td>9</td>
            <td>Сильная пыльная буря</td>
        </tr>
        <tr>
            <td>10</td>
            <td>Дымка</td>
        </tr>
        <tr>
            <td>11</td>
            <td>Морось</td>
        </tr>
        <tr>
            <td>12</td>
            <td>Ледяной дождь</td>
        </tr>
    </tbody>
</table>

##### Правила формирования рекомендаций по управлению ТС

- При одновременном появлении кодов рекомендаций 1 и 2, код 1 не добавляется;
- При одновременном появлении кодов рекомендаций 3 и 4, код 3 не добавляется;
- При одновременном появлении кодов рекомендаций 5 и 6, код 6 не добавляется;
- При одновременном появлении кодов рекомендаций 9 и 10, код 10 не добавляется.

<table border = "1">
      <thead>
        <th>
          Признак погодного явления
        </th>
        <th>
          Погодное явление
        </th>
        <th>
          Код
        </th>
        <th>
            Строка
        </th>
        <th>
          Рекомендация
        </th>
      </thead>
      <tbody>
        <tr>
          <td>weatherCondition = 1</td>
          <td>Метель</td>
          <td>1<br>4<br>5<br>7</rb></td></td>
          <td>KEEP_SPEED<br>
            INCREASE_DISTANCE<br>
            AVOID_ABRUPT_ACTIONS<br>
            TURN_ON_FOG_LIGHTS</td>
          <td>Соблюдайте скоростной режим<br>
            Увеличьте дистанцию до впереди движущихся ТС<br>
            Избегайте резких маневров и торможений<br>
            Включите противотуманные фары</td>
        </tr>
        <tr>
            <td>weatherCondition = 2</td>
            <td>Сильная метель</td>
            <td>2<br>4<br>5<br>6<br>7<br></td>
            <td>SLOW_DOWN<br>
            INCREASE_DISTANCE<br>
            AVOID_ABRUPT_ACTIONS<br>
            REFUSE_TO_OVERTAKE<br>
            TURN_ON_FOG_LIGHTS
            </td>
            <td>Снизьте скорость до безопасной<br>
            Увеличьте дистанцию до впереди движущихся ТС<br>
            Избегайте резких маневров и торможений<br>
            Откажитесь от перестроений и обгонов<br>
            Включите противотуманные фары</td>
        </tr>
        <tr>
            <td>weatherCondition = 3</td>
            <td>Гололед</td>
            <td>2<br>4<br>5<br>6</td>
            <td>SLOW_DOWN<br>
            INCREASE_DISTANCE<br>
            AVOID_ABRUPT_ACTIONS<br>
            REFUSE_TO_OVERTAKE
        </td>
            <td>Снизьте скорость до безопасной<br>
                Увеличьте дистанцию до впереди движущихся ТС<br>
                Избегайте резких маневров и торможений<br>
                Откажитесь от перестроений и обгонов
            </td>
        </tr>
        <tr>
            <td>weatherCondition = 4</td>
            <td>Сильный гололед</td>
            <td>2<br>4<br>5<br>6</td>
            <td>SLOW_DOWN<br>
                INCREASE_DISTANCE<br>
                AVOID_ABRUPT_ACTIONS<br>
                REFUSE_TO_OVERTAKE</td>
            <td>Снизьте скорость до безопасной<br>
                Увеличьте дистанцию до впереди движущихся ТС<br>
                Избегайте резких маневров и торможений<br>
                Откажитесь от перестроений и обгонов</td>
        </tr>
        <tr>
            <td>weatherCondition = 5</td>
            <td>Туман</td>
            <td>2<br>4<br>5<br>6<br>7</td>
            <td>SLOW_DOWN<br>
                INCREASE_DISTANCE<br>
                AVOID_ABRUPT_ACTIONS<br>
                REFUSE_TO_OVERTAKE<br>
                TURN_ON_FOG_LIGHTS</td>
            <td>Снизьте скорость до безопасной<br>
                Увеличьте дистанцию до впереди движущихся ТС<br>
                Избегайте резких маневров и торможений<br>
                Откажитесь от перестроений и обгонов<br>
                Включите противотуманные фары</td>
        </tr>
        <tr>
            <td>weatherCondition = 6</td>
            <td>Дым</td>
            <td>2<br>4<br>6<br>7</td>
            <td>SLOW_DOWN<br>
                INCREASE_DISTANCE<br>
                REFUSE_TO_OVERTAKE<br>
                TURN_ON_FOG_LIGHTS
            </td>
            <td>Снизьте скорость до безопасной<br>
                Увеличьте дистанцию до впереди движущихся ТС<br>
                Откажитесь от перестроений и обгонов<br>
                Включите противотуманные фары</td>
        </tr>
        <tr>
            <td>weatherCondition = 7</td>
            <td>Сильный туман</td>
            <td>2<br>4<br>5<br>6<br>7</td>
            <td>SLOW_DOWN<br>
                INCREASE_DISTANCE<br>
                AVOID_ABRUPT_ACTIONS<br>
                REFUSE_TO_OVERTAKE<br>
                TURN_ON_FOG_LIGHTS
            </td>
            <td>Снизьте скорость до безопасной<br>
                Увеличьте дистанцию до впереди движущихся ТС<br>
                Избегайте резких маневров и торможений<br>
                Откажитесь от перестроений и обгонов<br>
                Включите противотуманные фары
            </td>
            <tr>
                <td>weatherCondition = 8</td>
                <td>Пыльная буря</td>
                <td>2<br>4<br>6<br>7</td>
                <td>SLOW_DOWN<br>
                    INCREASE_DISTANCE<br>
                    REFUSE_TO_OVERTAKE<br>
                    TURN_ON_FOG_LIGHTS
                </td>
                <td>Снизьте скорость до безопасной<br>
                    Увеличьте дистанцию до впереди движущихся ТС<br>
                    Откажитесь от перестроений и обгонов<br>
                    Включите противотуманные фары
                </td>
            </tr>
            <tr>
                <td>weatherCondition = 9</td>
                <td>Сильная пыльная буря</td>
                <td>2<br>4<br>6<br>7</td>
                <td>SLOW_DOWN<br>
                    INCREASE_DISTANCE<br>
                    REFUSE_TO_OVERTAKE<br>
                    TURN_ON_FOG_LIGHTS</td>
                <td>Снизьте скорость до безопасной<br>
                    Увеличьте дистанцию до впереди движущихся ТС<br>
                    Откажитесь от перестроений и обгонов<br>
                    Включите противотуманные фары
                </td>
            </tr>
            <tr>
                <td>weatherCondition = 10</td>
                <td>Дымка</td>
                <td>2<br>4<br>6<br>7</td>
                <td>SLOW_DOWN<br>
                    INCREASE_DISTANCE<br>
                    REFUSE_TO_OVERTAKE<br>
                    TURN_ON_FOG_LIGHTS
                </td>
                <td>Снизьте скорость до безопасной<br>
                    Увеличьте дистанцию до впереди движущихся ТС<br>
                    Откажитесь от перестроений и обгонов<br>
                    Включите противотуманные фары
                </td>
            </tr>
            <tr>
                <td>weatherCondition = 11</td>
                <td>Морось</td>
                <td>2<br>4<br>5<br>6<br>7</td>
                <td>SLOW_DOWN<br>
                    INCREASE_DISTANCE<br>
                    AVOID_ABRUPT_ACTIONS<br>
                    REFUSE_TO_OVERTAKE<br>
                    TURN_ON_FOG_LIGHTS
                </td>
                <td>Снизьте скорость до безопасной<br>
                    Увеличьте дистанцию до впереди движущихся ТС<br>
                    Избегайте резких маневров и торможений<br>
                    Откажитесь от перестроений и обгонов<br>
                    Включите противотуманные фары
                </td>
            </tr>
            <tr>
                <td>weatherCondition = 12</td>
                <td>Ледяной дождь</td>
                <td>2<br>4<br>5<br>6</td>
                <td>SLOW_DOWN<br>
                    INCREASE_DISTANCE<br>
                    AVOID_ABRUPT_ACTIONS<br>
                    REFUSE_TO_OVERTAKE
                </td>
                <td>Снизьте скорость до безопасной<br>
                    Увеличьте дистанцию до впереди движущихся ТС<br>
                    Избегайте резких маневров и торможений<br>
                    Откажитесь от перестроений и обгонов
                </td>
            </tr>
            <tr>
                <td>hail = 1 или<br>hail = 2</td>
                <td>Град</td>
                <td>1<br>3<br>5</td>
                <td>KEEP_SPEED<br>
                    KEEP_DISTANCE<br>
                    AVOID_ABRUPT_ACTIONS
                </td>
                <td>Соблюдайте скоростной режим<br>
                    Соблюдайте дистанцию до впереди движущихся ТС<br>
                    Избегайте резких маневров и торможений
                </td>
            </tr>
            <tr>
                <td>stormPrediction = true</td>
                <td>Гроза</td>
                <td>10</td>
                <td>AVOID_PLACES_UNPROTECTED<br>_FROM_LIGHTNING</td>
                <td>Избегайте нахождение в незащищённых от молнии местах</td>
            </tr>
            <tr>
                <td>windType = 2 или<br>windType = 3</td>
                <td>Сильный ветер и штормовой ветер</td>
                <td>2</td>
                <td>SLOW_DOWN</td>
                <td>Снизьте скорость до безопасной</td>
            </tr>
            <tr>
                <td>windType = 4</td>
                <td>Ураганный ветер</td>
                <td>9</td>
                <td>WAIT_OUT_BAD_WEATHER_<br>IN_SHELTER</td>
                <td>Переждите плохую погоду в укрытии</td>
            </tr>
            <tr>
                <td>rainIntensity = 1</td>
                <td>Дождь</td>
                <td>1<br>3</td>
                <td>KEEP_SPEED<br>
                    KEEP_DISTANCE
                </td>
                <td>Соблюдайте скоростной режим<br>
                    Соблюдайте дистанцию до впереди движущихся ТС
                </td>
            </tr>
            <tr>
                <td>rainIntensity = 2</td>
                <td>Сильный дождь</td>
                <td>2<br>3<br>5</td>
                <td>SLOW_DOWN<br>
                    KEEP_DISTANCE<br>
                    AVOID_ABRUPT_ACTIONS
                </td>
                <td>Снизьте скорость до безопасной<br>
                    Соблюдайте дистанцию до впереди движущихся ТС<br>
                    Избегайте резких маневров и торможений
                </td>
            </tr>
            <tr>
                <td>rainIntensity = 3</td>
                <td>Очень сильный дождь</td>
                <td>2<br>4<br>5<br>6</td>
                <td>SLOW_DOWN<br>
                    INCREASE_DISTANCE<br>
                    AVOID_ABRUPT_ACTIONS<br>
                    REFUSE_TO_OVERTAKE
                </td>
                <td>Снизьте скорость до безопасной<br>
                    Увеличьте дистанцию до впереди движущихся ТС<br>
                    Избегайте резких маневров и торможений<br>
                    Откажитесь от перестроений и обгонов
                </td>
            </tr>
            <tr>
                <td>snowIntensity = 1</td>
                <td>Снег</td>
                <td>1<br>3<br>7</td>
                <td>KEEP_SPEED<br>
                    KEEP_DISTANCE<br>
                    TURN_ON_FOG_LIGHTS
                </td>
                <td>Соблюдайте скоростной режим<br>
                    Соблюдайте дистанцию до впереди движущихся ТС<br>
                    Включите противотуманные фары
                </td>
            </tr>
            <tr>
                <td>snowIntensity = 2</td>
                <td>Сильный снегопад</td>
                <td>2<br>4<br>5<br>7</td>
                <td>SLOW_DOWN<br>
                    INCREASE_DISTANCE<br>
                    AVOID_ABRUPT_ACTIONS<br>
                    TURN_ON_FOG_LIGHTS
                </td>
                <td>Снизьте скорость до безопасной<br>
                    Увеличьте дистанцию до впереди движущихся ТС<br>
                    Избегайте резких маневров и торможений<br>
                    Включите противотуманные фары
                </td>
            </tr>
            <tr>
                <td>snowIntensity = 3</td>
                <td>Очень сильный снегопад</td>
                <td>2<br>4<br>5<br>6<br>7</td>
                <td>SLOW_DOWN<br>
                    INCREASE_DISTANCE<br>
                    AVOID_ABRUPT_ACTIONS<br>
                    REFUSE_TO_OVERTAKE<br>
                    TURN_ON_FOG_LIGHTS
                </td>
                <td>Снизьте скорость до безопасной<br>
                    Увеличьте дистанцию до впереди движущихся ТС<br>
                    Избегайте резких маневров и торможений<br>
                    Откажитесь от перестроений и обгонов<br>
                    Включите противотуманные фары    
                </td>
            </tr>
            <tr>
                <td>isDay = false</td>
                <td>Наступление ночи</td>
                <td>8</td>
                <td>TURN_ON_LOW_BEAM</td>
                <td>Включите фары ближнего/дальнего света</td>
            </tr>
      </tbody>
    </table>

#### Характеристики скорости ветра

<table border = "1">
    <thead>
        <th>
            Шкала
        </th>
        <th>
            Диапазон скорости, м/с
        </th>
        <th>
            Качественная<br>хар-ка
        </th>
        <th>
            Иконка
        </th>
        <th>
            Примечание
        </th>
    </thead>
    <tbody>
        <tr>
            <td>
                0
            </td>
            <td>0 – 0,999</td>
            <td>Штиль</td>
            <td></td>
            <td>В описании<br>не выводится</td>
        </tr>
        <tr>
            <td>1</td>
            <td>1,0 – 14,999</td>
            <td>Слабый ветер</td>
            <td>
                <img src="./Icons/weak_wind" alt="weak_wind" width="70" height="40">
            </td>
            <td>В описании<br>не выводится</td>
        </tr>
        <tr>
            <td>2</td>
            <td>15,0 – 24,999</td>
            <td>Сильный ветер</td>
            <td>
                <img src="strong_wind.png" alt="strong_wind" width="70" height="40">
            </td>
            <td></td>
        </tr>
        <tr>
            <td>3</td>
            <td>25,0 – 32,999</td>
            <td>Штормовой ветер</td>
            <td><img src="storm.png" alt="storm" width="70" height="40"></td>
            <td></td>
        </tr>
        <tr>
            <td>4</td>
            <td>33 и более</td>
            <td>Ураган</td>
            <td>
                <img src="hurricane.png" alt="hurricane" width="70" height="40">
            </td>
            <td></td>
        </tr>
    </tbody>
</table>

#### Характеристики облачности

<table border="1">
    <thead>
        <th>Код</th>
        <th>Значение в %</th>
        <th>Характеристика</th>
        <th>Иконка день</th>
        <th>Иконка ночь</th>
    </thead>
    <tbody>
        <tr>
            <td>0</td>
            <td>0 – 10,99</td>
            <td>Ясно</td>
            <td>
                <img src="brightly.png" alt="brightly" width="70" height="50">
            </td>
            <td>
                <img src="brightly_night-1.png" alt="brightly_night" width="70" height="50">
            </td>
        </tr>
        <tr>
            <td>1</td>
            <td>11 – 24,99</td>
            <td>Малооблачно</td>
            <td>
                <img src="low_brightly.png" alt="slightly_cloudy" width="70" height="40">
            </td>
            <td>
                 <img src="partly_cloudy_night.png" alt="slightly_cloudy_night" width="70" height="40">
            </td>
        </tr>
        <tr>
            <td>2</td>
            <td>25 – 50,99</td>
            <td>Облачно с прояснениями</td>
            <td>
                <img src="cloudy.png" alt="cloudy" width="70" height="40">
            </td>
            <td>
                <img src="cloudy_night.png" alt="cloudy_night" width="70" height="40">
            </td>
        </tr>
        <tr>
            <td>3</td>
            <td>51 – 84,99</td>
            <td>Облачно</td>
            <td>
                <img src="cloudy_cloudy.png" alt="cloudy" width="70" height="40">
            </td>
            <td>
                <img src="cloudy_cloudy_night.png" alt="cloudy_night" width="70" height="40">
            </td>
        </tr>
        <tr>
            <td>4</td>
            <td>85 - 100</td>
            <td>Пасмурно</td>
            <td>
                <img src="4.png" alt="overcast" width="70" height="40">
            </td>
            <td>
                <img src="4-2.png" alt="overcast_night" width="70" height="40">
            </td>
        </tr>
    </tbody>
</table>

#### Характеристики жидких осадков

<table border="1">
    <thead>
        <th>Шкала</th>
        <th>Кол-во осадков</th>
        <th>Интенсивность</th>
        <th>Иконка</th>
        <th>Примечание</th>
    </thead>
    <tbody>
        <tr>
            <td>0</td>
            <td>0 – 0,299</td>
            <td>Без осадков</td>
            <td>-</td>
            <td>В описании не<br>выводится</td>
        </tr>
        <tr>
            <td>1</td>
            <td>0,3 ‒ 14,999</td>
            <td>Дождь</td>
            <td>
                 <img src="1.png" alt="rain" width="70" height="50">
            </td>
            <td></td>
        </tr>
        <tr>
            <td>2</td>
            <td>15 – 29,999</td>
            <td>Сильный дождь</td>
            <td>
                <img src="2.png" alt="strong_rain" width="70" height="50">
            </td>
            <td></td>
        </tr>
        <tr>
            <td>3</td>
            <td>>=30</td>
            <td>Очень сильный<br>дождь</td>
            <td>
                <img src="3.png" alt="strongest_rain" width="70" height="50">
            </td>
            <td></td>
        </tr>
    </tbody>
</table>

#### Характеристики твердых осадков

<table border="1">
    <thead>
        <th>Шкала</th>
        <th>Кол-во осадков</th>
        <th>Интенсивность</th>
        <th>Иконка</th>
        <th>Примечание</th>
    </thead>
    <tbody>
        <tr>
            <td>0</td>
            <td>0 – 0,199</td>
            <td>Без осадков</td>
            <td>-</td>
            <td>В описании не<br>выводится</td>
        </tr>
         <tr>
            <td>1</td>
            <td>0,2 ‒ 6,999</td>
            <td>Снег</td>
            <td>
                 <img src="snow1.png" alt="snow" width="70" height="40">
            </td>
            <td></td>
        </tr>
         <tr>
            <td>2</td>
            <td>7 – 19,999</td>
            <td>Сильный снегопад</td>
            <td>
                <img src="snow2-1.png" alt="snow2" width="70" height="40">
            </td>
            <td></td>
        </tr>
         <tr>
            <td>3</td>
            <td>>=20</td>
            <td>Очень сильный<br>снегопад</td>
            <td>
                <img src="snow3.png" alt="snow3" width="70" height="40">
            </td>
            <td></td>
        </tr>
    </tbody>
</table>

#### Характеристики смешанных осадков

<table border="1">
    <thead>
        <th>Интенсивность<br>дождя</th>
        <th>Интенсивность<br>снега</th>
        <th>Интенсивность</th>
        <th>Иконка</th>
    </thead>
<tbody>
    <tr>
        <td>1</td>
        <td>1</td>
        <td>Снег с дождем</td>
        <td>
            <img src="mixed1.png" alt="mixed_precipitation" width="70" height="40">
        </td>
    </tr>
    <tr>
        <td>1</td>
        <td>2</td>
        <td>Сильный снегопад<br>с дождем</td>
        <td>
            <img src="mixed2.png" alt="mixed_precipitation" width="70" height="40">
        </td>
    </tr>
    <tr>
        <td>1</td>
        <td>3</td>
        <td>Очень сильный<br>снегопад с дождем</td>
        <td>
            <img src="mixed3.png" alt="mixed_precipitation" width="80" height="40">
        </td>
    </tr>
    <tr>
        <td>2</td>
        <td>1</td>
        <td>Сильный дождь<br>со снегом</td>
        <td>
            <img src="mixed4.png" alt="mixed_precipitation" width="70" height="40">
        </td>
    </tr>
    <tr>
        <td>2</td>
        <td>2</td>
        <td>Сильный снегопад<br>с дождем</td>
        <td>
            <img src="mixed5.png" alt="mixed_precipitation" width="70" height="40">
        </td>
    </tr>
    <tr>
        <td>2</td>
        <td>3</td>
        <td>Очень сильный<br>снегопад с дождем</td>
        <td>
            <img src="mixed6.png" alt="mixed_precipitation" width="80" height="40">
        </td>
    </tr>
    <tr>
        <td>3</td>
        <td>1</td>
        <td>Очень сильный<br>дождь со снегом</td>
        <td>
            <img src="mixed8.png" alt="mixed_precipitation" width="80" height="40">
        </td>
    </tr>
    <tr>
        <td>3</td>
        <td>2</td>
        <td>Очень сильный<br>дождь со снегом</td>
        <td>
            <img src="mixed9.png" alt="mixed_precipitation" width="80" height="40">
        </td>
    </tr>
    <tr>
        <td>3</td>
        <td>3</td>
        <td>Очень сильный<br>снегопад с дождем</td>
        <td>
            <img src="mixed9.png" alt="mixed_precipitation" width="80" height="40">
        </td>
    </tr>
</tbody>
</table>

#### Характеристики града

<table border="1">
    <thead>
        <th>Шкала</th>
        <th>Величина мм</th>
        <th>Интенсивность</th>
        <th>Иконка</th>
        <th>Примечание</th>
    </thead>
    <tbody>
        <tr>
            <td>0</td>
            <td>0</td>
            <td>Нет града</td>
            <td>-</td>
            <td>В описании<br>не выводится</td>
        </tr>
        <tr>
            <td>1</td>
            <td><20</td>
            <td>Град</td>
            <td>
                <img src="hail1.png" alt="hail" width="80" height="40">
            </td>
            <td></td>
        </tr>
        <tr>
            <td>2</td>
            <td>>=20</td>
            <td>Крупный град</td>
            <td>
                <img src="hail2.png" alt="hail" width="80" height="40">
            </td>
            <td></td>
        </tr>
    </tbody>
</table>

#### Характеристики направления ветра

<table border="1">
    <thead>
        <th>Градусы<br>метеорологические</th>
        <th>Обозначение</th>
        <th>Вывод</th>
    </thead>
    <tbody>
        <tr>
            <td>
                337.5 – 360<br>0 – 22.499
            </td>
            <td>Северный</td>
            <td>C</td>
        </tr>
        <tr>
            <td>22.5 – 67.499</td>
            <td>Северо-восточный</td>
            <td>СВ</td>
        </tr>
        <tr>
            <td>67.5 – 112.499</td>
            <td>Восточный</td>
            <td>В</td>
        </tr>
        <tr>
            <td>112.5 – 157.499</td>
            <td>Юго-восточный</td>
            <td>ЮВ</td>
        </tr>
        <tr>
            <td>157.5 – 202.499</td>
            <td>Южный</td>
            <td>Ю</td>
        </tr>
        <tr>
            <td>202.5 – 247.499</td>
            <td>Юго-западный</td>
            <td>ЮЗ</td>
        </tr>
        <tr>
            <td>247.5 - 292.499</td>
            <td>Западный</td>
            <td>З</td>
        </tr>
        <tr>
            <td>292.5 – 337.499</td>
            <td>Северо-западный</td>
            <td>СЗ</td>
        </tr>
    </tbody>
</table>
