# Android

@startuml

Alice -> Bob: Authentication Request
Bob --> Alice: Authentication Response

Alice -> Bob: Another authentication Request
Alice <-- Bob: another authentication Response
@enduml

Зачем я дублирую на рис. 13.4 исходный код двух предыдущих листингов? Чтобы вы видели, что у среды нет никаких замечаний к коду. Если же у вас что-то подчеркнуто или имеется какое-либо предупреждение, значит, вы где-то допустили ошибку. Например, если забыть импортировать android.util.Log, то Android Studio сразу обратит на этот факт ваше внимание (рис. 13.5).

## 13.3. Работа с базой данных

### 13.3.1. Создание базы данных

Половина этой главы была потрачена на написания вспомогательного класса, но до результата еще далеко. Поэтому самое время перейти на нашу главную деятельность (activity) и изменить ее метод onCreate() (рис. 13.6):

    @Override
    protected void onCreate(Bundle savedinstanceState) {
        super.onCreate(savedinstanceState);
        setContentView(R.layout.activity_main);

        // Инициализируем наш вспомогательный класс
        MyDataBase mydЬ new MyDataBase(this);

        // Открываем БД для чтения и записи
        SQLiteDatabase.sqdЬ = mydЬ.getWritaЬleDatabase();

        // Закрываем соединения с базой данных
        sqdЬ. close () ;
        mydЬ.close();
    }

# Рисунок
Рис. 13.6. Код для главной деятельности

Теперь запустите проект. Вам покажется, что ничего не произошло, но на самом деле в каталоге \data\data\com.example.den.android_db\databases эмулятора (или реального устройства — смотря как вы предпочитаете запускать приложения) появится файл my_database.db.
<br>Чтобы убедиться в этом, командой меню View|Tool Windows откройте Device File Explorer и просмотрите структуру файлов виртуального устройства (рис. 13.7).

# Рисунок
Рис. 13.7. База данных создана

<br>На этом подготовка базы данных к работе завершена, и мы можем начать с ней работать, т. е. вставлять в нее данные и извлекать их.
### 13.3.2 Вставка записей
Для вставки записей в БД можно использовать или класс ContentValues, или обычный SQL-запрос INSERT. Если вы раньше имели дело с SQL (хоть в PHP, хоть в любом другом языке программирования), то вам будет проще использовать именно SQL-запрос INSERT.
<br>Вот пример вставки данных с использованием SQL-запроса INSERT:

    String insertQuery = "INSERT INTO" + 
        MyDataBase.TAВLE_NAМE + " (" + MyDataBase.UNAМE + ") VALUES ('" + txtData.getText().toString() + "')";
        sqdЬ.execSQL(insertQuery);

Тот же код, но в сокращенном виде:

    String insertQuery = "SQL-зaпpoc";
    sqdЬ.execSQL(insertQuery); // выполняем запрос

### 13.3.3. Чтение данных
Прочитать данные из базы данных можно или методом query (), или методом
rawQuery(). Рассмотрим сначала первый метод:

    Cursor cursor = sqdЬ.query(MyDataBase.TAВLE_NAМE, new String[] {
                         MyDataBase. ID, MyDataBase.UNAМE ),
                         null, // Колонки для WНЕRЕ-части
                         null, // Значения для WНЕRЕ-части
                         null, // Не группировать строки
                         null, // Не фильтровать группы строк
                         null // Порядок сортировки
                         ) ;
           while (cursor.moveToNext()) {
                  // Получаем индексы колонки и значения этих колонок
                  int id = cursor.getint(cursor.getColumnindex(MyDataBase._ID));
                  String name = cursor.getString(cursor
                                .getColumnIndex(MyDataBase.CATNAМE));
                  Log.i("LOG_TAG", "ROW " + id + " НАS UNAМE " + name);
            }
            cursor.close();
        
Если вы внимательно прочитали комментарии и знакомы со структурой оператора
SELECT, метод query() должен быть вам понятен. Мы сформировали запрос и передали его базе данных. В ответ мы получаем все колонки, которые соответствуют
запросу, и выводим их в журнал с помощью Log.i().
<br>Метод rawQuery() отличается тем, что ему можно непосредственно передать SQLзanpoc: 

    String query = "SELECT " + MyDataBase._ID + ", "
                              + MyDataBase.UNAМE + " FROМ " + MyDataBase.TAВLE_NAME;
        Cursor cursor2 = sqdЬ.rawQuery(query, null);

        while (cursor2.moveToNext()) {
          int id = cursor2.getInt(cursor2.getColumnIndex(MyDataBase._ID));
          String name = cursor2.getString(cursor2.getColumnIndex(MyDataBase.UNAМE));
          Log.i(" LOG_TAG" , "ROW " + id + " НАS UNAМE" + name);
        }
        cursor2.close();

Здесь мы сначала формируем запрос. Затем передаем его методу rawQuery(). После
этого читаем все строки результата с помощью метода moveToNext(). Получаем
результаты и выводим их с помощью Log.i() в журнал.
                                          * * *
Итак, мы научились создавать базу данных, вставлять в нее данные и извлекать их
из нее. Извлеченные данные мы выводили в журнал, просмотреть который можно
с помощью среды Android Studio. В реальных же приложениях вы вольны делать с данными все, что пожелаете, - например, вывести их в TextView.

# Глава 14

# Создание анимации

## 14.1. Анимация преобразований
В Android используются два подхода к созданию анимации: кадровая анимация (Frame Animation) и анимация преобразований (Tween Animation). Сначала мы рассмотрим анимацию преобразований, а затем - кадровую анимацию.
<br>Анимация преобразований выполняется путем выполнения над изображением ряда преобразований: вращения, добавления прозрачности, изменения размера и т. п. В пакете android.view.animationpackages имеются все необходимые для выполнения преобразований классы:
<br>□ Animationset (ХМL-элемент <set>) - представляет группу анимаций, которые запускаются вместе;
<br>□ AlphaAnimation (ХМL-элемент <alpha>) - управляет прозрачностью объекта;
<br>□ RotateAnimation (ХМL-элемент <rоtаtе>) - управляет вращением объекта;
<br>□ ScaleAnimation (ХМL-элемент <scale>) - управляет масштабированием (изменением размера) объекта;
<br>□ TranslateAnimation (ХМL-элемент <translate>) - управляет позиционированием объекта.
<br>Анимацию преобразования можно создать как в ХМL-файле, так и в программном коде. Начнем с ХМL-файла. Файл анимации помещают в каталог res\anim. В этом файле имеется единственный корневой элемент. Таким элементом может быть один из элементов преобразования: <rotate>, <alpha>, <scale>, <translate> или <set>, который является контейнером для этих четырех элементов (он позволяет задать несколько преобразований).
<br>По умолчанию все описанные в <set> преобразования выполняются одновременно. Чтобы они выполнялись последовательно (в порядке описания в ХМL-файле), нужно использовать атрибут startOffset, задающий задержку между выполнением преобразований в миллисекундах: 
android:startOffset="l000" 
У всех элементов преобразований есть общие атрибуты, описанные в табл. 14.1.
####Таблица 14.1. Общие атрибуты элементов преобразований
# Таблица

<br>Теперь рассмотрим собственные атрибуты элементов преобразования.
<br>□ У элемента <set> есть атрибут shareInterpolator. Если он равен true, то интерполятор, заданный атрибутом interpolator для <set>, будет использоваться для всех дочерних элементов преобразования, входящих в этот элемент <set>.
<br>□ У элемента <alpha> есть атрибуты fromAlpha и toAlpha. Первый задает начальное значение прозрачности объекта, второй - конечное. Значения прозрачности находятся в диапазоне от О до 1, где О - полная прозрачность объекта (объект почти невидим).
<br>□ У элемента <translate>, создающего вертикальную или горизонтальную анимацию, атрибутов больше:
<br>• fromXDelta - начальное положение по оси .Х;
<br>• toXDelta - конечное положение по оси Х;
<br>• fromYDelta - начальное положение по оси У;
<br>• toYDelta. - конечное положение по оси У.
<br>Атрибуты могут принимать либо абсолютное значение, либо значение в процентах: от -100 до 100%. Можно также указывать процент относительно родителя:
от -100%р до 100%р.
<br>□ Элемент rotate служит для анимации вращения и поддерживает атрибуты:
<br>• fromDegrees - начальный угол вращения в градусах;
<br>• toDegrees - конечный угол вращения в градусах;
<br>• pivotX - задает координату Х центра вращения в пикселах;
<br>• pivotY - задает координату У центра вращения в пикселах.
<br>□ Элемент <scale> управляет изменением размера объекта. Вы можете использовать следующие атрибуrы:
<br>• fromXScale - начальный масштаб по оси Х;
<br>• toXScale - конечный масштаб по оси Х;
<br>• fromYScale - начальный масшrаб по оси У;
<br>• toYScale - конечный масштаб по оси У;
<br>• pivotX - координата (Х) закременного центра;
<br>• pivotY - координата (У) закременного центра.
<br>Вернемся к практике. Прежде всего нужно создать ХМL-файл анимации в каталоге res\anim. В листинге 14.1 приведен простой файл анимации, содержащий только элемент <alpha> внуrри элемента <set>. В листинге 14.2 приведена более сложная анимация, т. к. задано два анимационных эффекта.
####Листинг 14.1 Файл res\anim\alpha.xml

    <?xml version="l.0" encoding="utf-8"
    <set xmlns:android="http://schemas.android.com/apk/res/android"
    android:shareInterpolator="false">
    <alpha
            android:fromAlpha="0.0"
            android:toAlpha="l.0"
            android:startOffset="0"
            android:duration="3000"/>
    </set>

####Листинг 14.2 Файл res\anim\advanced.xml

    <?xml version="l.0" encoding="utf-8"
    <set xmlns:android="http://schemas.android.com/apk/res/android"
    android:shareInterpolator="false">
    <alpha
            android:fromAlpha="0.0"
            android:toAlpha="l.0"
            android:startOffset="0"
            android:duration="З000"/>
    <translate
            android:toYDelta="-100"
            android:fillAfter="true"
            android:duration="2500"/>
    </set>

<br>Теперь займемся Jаvа-кодом. Сначала нужно подключить необходимые пакеты. Далее- загрузить картинку, над которой будет производиться анимация. Затем загрузить саму анимацию. Это можно сделать методом loadAnimation() из AnimationUtils. После чего запустить анимацию методом StartAnimation(). Весь этот процесс описан в листинге 14.3.
####Листинr 14.3. Создание анимации

    // Подключаем необходимые пакеты
    import android.view.animation.Animation;
    import android.view.animation.AnimationUtils;
    import android.view.animation.Animation.AnimationListener;
    ...
    // Загружаем картинку
    ImageView image = (ImageView)findViewById(R.id.image);
    // Загружаем анимацию из файла alpha.xml
    Animation animation = AnimationUtils.loadAnimation(this, R.anim.alpha);
    // Запускаем анимацию
    image.startAnimation(animation);

<br>Как видите, ничего сложного в анимации преобразования нет. Необходимо только подобрать нужный эффект (или группу эффектов).
## 14.2. Традиционная кадровая анимация
Традиционная кадровая анимация представляет собой последовательную смену различных изображений - подобно киноленте. Основой для этого типа анимации является класс AnimationDrawable.
<br>Как и в случае с анимацией преобразований, начнем создавать анимацию с ХМL-файла, который, как обычно, нужно поместить в каталог res\anim. В листинге 14.4 приведен пример такого файла.
#### Листинr 14.4. Файt res\anim\frames.xml

    <animation-list xmlns:android="http://schemas.android.com/apk/res/android"
    android:oneshot="false">
    <item android:drawable="@drawable/frame1" android:duration="100"/>
    <item android:drawable="@drawable/frame2" android:duration="100"/>
    <item android:drawaЬle="@drawable/frameЗ" android:duration="100"/>
    <item android:drawaЬle="@drawable/frame4" android:duration="100"/>
    <item android:drawaЬle="@drawable/frame5" android:duration="100"/>
    </animation-list>

<br>У нас есть пять кадров (@drawable/frame1 - @drawable/frame5), длительность отображения каждого кадра- 100 мс. Если параметр android:oneshot установлен
