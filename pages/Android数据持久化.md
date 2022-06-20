- date: [[2018-10-30]]
- tags: [[Android]]
##    文件存储
### 写入文件

调用Context类中openFileOutput方法 返回一个FileOutputStream
![KN~Z_B8_H~W`AEKF4TMN)%O.png](https://www.dropbox.com/s/ziymnixtht413hg/KN~Z_B8_H~W%60AEKF4TMN%29%25O.png?dl=0&raw=1)
openFileOutput（文件名（不允许包含路径，默认存储到/data/data/<packagename>/file/),操作模式：MODE_PRIVATE（方法默认的模式 覆盖） or MODE_APPEND（追加）.

```java
  private void save() {
      String data = "Data";
      FileOutputStream out = null;
      BufferedWriter writer = null;
      try {
          out = openFileOutput("data", Context.MODE_APPEND);
          writer = new BufferedWriter(new OutputStreamWriter(out));
          writer.write(data);
      } catch (IOException e) {
          e.printStackTrace();
      }finally {
          if (writer != null){
              try {
                  writer.close();
              } catch (IOException e) {
                  e.printStackTrace();
              }
          }
      }
  }
```

可以通过device file explorer（在右下角）或Android Device Monitor(权限解决办法：https://www.jianshu.com/p/d8a9a2918c61)查看data中的文件 实在没办法解决 就用回api24
### 读取文件

![OKW4PN(T7C5EW_67BXFJ5YP.png](https://www.dropbox.com/s/7fumv5cptdcfeli/OKW4PN%28T7C5EW_67BXFJ5YP.png?dl=0&raw=1)

````java
  private String load()
      FileInputStream in = null;
      BufferedReader reader =null;
      StringBuilder content = new StringBuilder();
      try {
          in = openFileInput("data");
          reader = new BufferedReader(new InputStreamReader(in));
          String line="";
          while ((line = reader.readLine())!=null){
              content.append(line);
          }
      } catch (IOException e) {
          e.printStackTrace();
      }finally {
          if (reader!=null){
              try {
                  reader.close();
              } catch (IOException e) {
                  e.printStackTrace();
              }
          }
      }
      return content.toString();

  }
````
##   SharedPreferences 存储

SharedPreferences是使用键值对形式存储数据，使用Xml格式管理数据。
### 获取SharedPreferences对象的三个方式：
- Context类中的getSharedPreferences（）方法
  第一个参数指定SharedPreferences文件名称 第二个参数指定模式（除MODE_PRIVATE其他模式均废除）
- Activity类中getPreferences（）方法
  仅接受 mode 参数 (0)，使用当前活动类名作为SharedPreferences文件名
- PreferenceManager类中getDefaultSharedPreferences（）方法
  接收一个Context对象，使用当前当前应用程序包名作为SharedPreferences文件名
### 使用SharedPreferences存储数据
- 1.调用SharePreferences对象的edit（）方法获取一个SharePreferences.Editor对象
- 2.向SharePreferences.Editor 对象添加数据。例如 添加一个String对象用 putString()方法。
- 3.调用apply（）方法提交数据。完成存储。
### 使用SharedPreferences读取数据
- 1.获取SharePreferences对象方法
- 2.调用SharePreferences对象的getString()等方法获取数据。
  一个记住密码的Demo 
  
  ````java
  public class MainActivity extends AppCompatActivity {
  
    private SharedPreferences preferences;
    private EditText name;
    private EditText passwordEdit;
    private CheckBox rememberpass;
    private Button login;
  
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        preferences = PreferenceManager.getDefaultSharedPreferences(this);
        name =   (EditText)findViewById(R.id.name);
        passwordEdit = (EditText)findViewById(R.id.password);
        rememberpass = (CheckBox)findViewById(R.id.remember_password);
        login = (Button)findViewById(R.id.button);
        boolean isRemember = preferences.getBoolean("remember_password",false);
        if(isRemember){
            String account =preferences.getString("account","");
            String password =preferences.getString("password","");
            name.setText(account);
            passwordEdit.setText(password);
            rememberpass.setChecked(true);
        }
        login.setOnClickListener(new View.OnClickListener() {
  
            private SharedPreferences.Editor editor;
  
            @Override
            public void onClick(View v) {
                String account =name.getText().toString();
                String password =passwordEdit.getText().toString();
                if(account.equals("admin")&&password.equals("123456")){
                    editor = preferences.edit();
                    if(rememberpass.isChecked()){
                        editor.putBoolean("remember_password",true);
                        editor.putString("account",account);
                        editor.putString("password",password);
                    }else {
                        editor.clear();
                    }
                    editor.apply();
                    Toast.makeText(MainActivity.this,"登陆成功！",Toast.LENGTH_LONG).show();
                }else {
                    Toast.makeText(MainActivity.this,"登陆失败！",Toast.LENGTH_LONG).show();
                }
            }
        })
  }
  ````
##    SQLite 数据库存储
### 构建SQLiteOpenHelper  （管理SQLite的帮助类）
- SQLiteOpenHelper（）
  ![E`)NJZNT`LE5%%YJH9AC`1L.png](https://www.dropbox.com/s/wqhbtbb8pmkhuu5/E%60%29NJZNT%60LE5%25%25YJH9AC%601L.png?dl=0&raw=1)
  然而并不知道第三个参数Cursor是什么鬼东西，一般调用时传入null；
  使用该抽象类需要继承这个类并重写OnCreate（）与onUpgrade（）两个抽象方法；
- OnCreate（SQLiteDatabase db）
  Called when the database is created for the first time. This is where the creation of tables and the initial population of the tables should happen.
- onUpgrade (SQLiteDatabase db, int oldVersion, int newVersion)
  Called when the database needs to be upgraded. The implementation should use this method to drop tables, add tables, or do anything else it needs to upgrade to the new schema version.
### 创建数据库

使用getWritableDatabase()或getReadableDatabase（）创建数据库。但由于这两个方法需要较长时间才能返回，不建议在主线程（UI线层）中使用。
创表语句

````
create table Book (
          id integer primary key autoincrement, 
          author text, 
          price real, 
          pages integer, 
          name text)
          // blob 二进制类型 real 浮点数类型
````

Demo

````java
public class MyDatabaseHelper extends SQLiteOpenHelper {

  public static final String CREATE_BOOK = "create table Book ("
          + "id integer primary key autoincrement, "
          + "author text, "
          + "price real, "
          + "pages integer, "
          + "name text)";

  private Context mContext;

  public MyDatabaseHelper(Context context, String name,
                          SQLiteDatabase.CursorFactory factory, int version) {
      super(context, name, factory, version);
      mContext = context;
  }

  @Override
  public void onCreate(SQLiteDatabase db) {
      db.execSQL(CREATE_BOOK);
      Toast.makeText(mContext, "Create succeeded", Toast.LENGTH_SHORT).show();
  }

  @Override
  public void onUpgrade(SQLiteDatabase db, int oldVersion, int newVersion) {
      db.execSQL("drop table if exists Book");
      db.execSQL("drop table if exists Category");
      onCreate(db);
  }
}
````

MainActivity 中的调用

````
      MyDatabaseHelper dbHelper = new MyDatabaseHelper(this, "BookStore.db", null, 1);
      dbHelper.getWritableDatabase();
````
### 升级数据库
- 修改SQLiteOpenHelper构造方法中第四个参数比之前大 就会调用onUpgrade（）方法。
- 修改onUpgrade()方法
  
  ````
    @Override
    public void onUpgrade(SQLiteDatabase db, int oldVersion, int newVersion) {
        db.execSQL("drop table if exists Book");
        onCreate(db);
    //如果存在 Book表就将其删除，并调用onCreate(db)重新创建。
    }
  ````
## CRUD

借助使用getWritableDatabase()或getReadableDatabase（）创建数据库返回的SQLiteDatabase对象进行CRUD操作。
##### 查询数据（Retrieve）
- insert (String table,String nullColumnHack, ContentValues values)
  利用ContentValues中put()方法重载添加数据
  
  ````
                SQLiteDatabase db = dbHelper.getWritableDatabase();
                ContentValues values = new ContentValues();
                // 开始组装第一条数据
                values.put("name", "The Da Vinci Code");
                values.put("author", "Dan Brown");
                values.put("pages", 454);
                values.put("price", 16.96);// id 列被设为自增长，不需要赋值
                db.insert("Book", null, values); // 插入数据
                values.clear();//清空values
  ````
##### 更新数据（Updata）
- update (String table,ContentValues values,String whereClause, String[] whereArgs)
  ContentValues values：装更新的数据
  String whereClause, String[] whereArgs：指定更新的某一行或某几行。（默认更新所有行）
  
  ````
  SQLiteDatabase db = dbHelper.getWritableDatabase();
                ContentValues values = new ContentValues();
                values.put("price", 10.99);
                db.update("Book", values, "name = ?", new String[] { "The Da Vinci Code" });//占位符 ? 通过 new String[] { "The Da Vinci Code" } 提供的字符串为内容
  ````
##### 删除数据（Delete）
- delete (String table,String whereClause,String[] whereArgs)
  String whereClause, String[] whereArgs：指定删除的某一行或某几行。（默认删除所有行）
  
  ````
                SQLiteDatabase db = dbHelper.getWritableDatabase();
                db.delete("Book", "pages > ?", new String[] { "500" });//删除pages>500的数据
  ````
##### 查询数据（Create）

![ZEIOM6D$QR6X@((AHXFIP09.png](https://www.dropbox.com/s/x83afp2zgh4skmn/ZEIOM6D%24QR6X%40%28%28AHXFIP09.png?dl=0&raw=1)
强行翻译~~~
直接上简单粗暴的代码

````
SQLiteDatabase db = dbHelper.getWritableDatabase();
              // 查询Book表中所有的数据
              Cursor cursor = db.query("Book", null, null, null, null, null, null);
              if (cursor.moveToFirst()) {
                  do {
                      // 遍历Cursor对象，
                      String name = cursor.getString(cursor.getColumnIndex("name"));
                      String author = cursor.getString(cursor.getColumnIndex("author"));
                      int pages = cursor.getInt(cursor.getColumnIndex("pages"));
                      double price = cursor.getDouble(cursor.getColumnIndex("price"));
                  } while (cursor.moveToNext());
              }
              cursor.close();
````
##### 直接使用SQL操作数据库
- db.execSQL("INSERT INTO table_name (列1, 列2,...) VALUES (?,?,?,?)",new String[]{"值1", "值2",....});
- db.execSQL(updata table_name set 列1=?  列2=?  ..., new String[]{"值1", "值2",....});
- db.execSQL(delete from table_name where set 列1 > "?", new String[]{"值"});
- db.rawQuery("select * from Book",null);
  ##LitePal 操作数据库