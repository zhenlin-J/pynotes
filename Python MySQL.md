![python访问mysql流程](".\images\python访问mysql流程.png") <br>

#### 简单查询流程demo <br>

```python
import pymysql as sql

def main():
    # 创建connection连接🔗
    conn = sql.connect(host='localhost',port=3306,user='root',password='9527',database='jingdong',charset='utf8')
    # 获取cursor对象
    csl = conn.cursor()
    count = csl.execute('select * from goods;') # 这里这一步实际上仅是获取了这个对象的内存位置
    print(count)
    print(csl.fetchone()) # .fetchone()返回的是一个记录 是一个元组
    print(csl.fetchall())
    # cursor 光标 这个对象的特点就是会记录下你当前操作停留的位置
    count = csl.execute("delete from goods where id = 6;")

    '''提交之前进行过的execute里的SQL语句 这里不提交是无法对数据库作出修改的'''
    conn.commit()
    # 关闭光标对象
    csl.close()
    # 关闭连接对象
    conn.close()

if __name__ == '__main__':
    main()
```

### 面向对象的思路demo <br>

```python
'''面向对象的编程思路'''
from pymysql import connect

class JD(object):
    def __init__(self):
        self.conn = connect(host='localhost', port=3306, user='root', password='9527', database='jingdong', charset='utf8')  # 这种不会变但是要多次使用的数据有三种地方可以存：类对象 实例对象(main函数) 全局变量(能不用就不用) 局部变量 这里显然放在实例对象里面会更加好，因为以后的类名可能有很多个 这里先使用面向对象的编程思路 放在类名下面
        self.cursor = self.conn.cursor()

    def __del__(self):
        self.cursor.close()
        self.conn.close()

    def execute_sql(self,sql):
        self.cursor.execute(sql) # 这里直接使用cursor
        for temp in self.cursor.fetchall():
            print(temp)

    @ staticmethod
    def print_menu(): # 这个对象不需要实例对象 不需要类对象 是一个静态方法 要去掉self 转为静态方法
        print("--------京东---------")
        print("1.所有的商品")
        print("2.所有的商品分类")
        print("3.所有的商品品牌分类")
        print("4.添加一个商品分类")
        return input("请输入功能对应的序号：")

    def show_all_items(self):
        '''显示所有的商品'''
        sql = 'select * from goods;'
        self.execute_sql(sql)

    def show_cates(self):
        '''显示所有的商品分类'''
        sql = 'select name from goods_cates;'
        self.execute_sql(sql)

    def show_brands(self):
        '''显示所有的商品品牌分类'''
        sql = 'select name from goods_brands;'
        self.execute_sql(sql)

    def add_brands(self):
        brand_name = input("请输入品牌的名字……")
        sql = f'insert into goods_brands (name) values("{brand_name}");'
        # self.execute_sql(sql) # 上面遍历使用的激活方法这里不再适用了
        self.cursor.execute(sql)
        self.conn.commit()

    def run(self):
        while True:
            ob = self.print_menu() # 实例对象可以调用任何方法：实例方法 类方法 静态方法 类对象只能调用类方法或者静态方法
            if ob == '1':
                # 查询所有的商品 这里的框架搭好了就可以开始写代码，注意这里最好在一个类下面就将所有相关的事情做完，让main函数封装的更加漂亮
                self.show_all_items() # 这里先直接写上需要的函数名 方法名 然后再去根据需要去创建
            elif ob == '2':
                # 查询分类
                self.show_cates()
            elif ob == '3':
                # 查询品牌
                self.show_brands()
            elif ob == '4':
                # 添加品牌分类
                self.add_brands()
            else:
                print("输入有误，请重新输入...")

def main(): # 实例对象 main函数是封装函数，这个封装函数越简单越好
    # 1.创建一个京东商城对象
    jd = JD()
    # 2.调用这个对象的run方法，让其运行
    jd.run()

if __name__ == '__main__':
    main()
```
上面只是对数据库的查询演示，查询是不需要提交数据的，但是对于增删改这些命令，都需要在敲完命令后，执行 conn.cmmit()命令，所有的SQL语句均会导入并真正执行，在命令行中可能会存在某些SQL语句错误，或者不想要，这时候要执行 conn.rollback()的操作，再将所有的SQL语句全部重新导入，确认无误后这时候再去执行 conn.cmmit()命令。注意这种行为下，主键的自动增长还是会生效，不要修改或者回滚这个自动增长的效果，这个是为了处理并发的情况下设置的。 <br>
在python脚本中，因为不是命令行，就只需要在写完代码后，好好检查一遍就好了。python脚本写好后就可以很方便的操纵数据库了。 <br>
实际中发现在python3中写SQL语句，这个;可以加也可以不加，没有啥子区别 <br>

### SQL注入 <br>

```python
'''面向对象的编程思路'''
from pymysql import connect

class JD(object):
    def __init__(self):
        self.conn = connect(host='localhost', port=3306, user='root', password='9527', database='jingdong', charset='utf8')  # 这种不会变但是要多次使用的数据有三种地方可以存：类对象 实例对象(main函数) 全局变量(能不用就不用) 局部变量 这里显然放在实例对象里面会更加好，因为以后的类名可能有很多个 这里先使用面向对象的编程思路 放在类名下面
        self.cursor = self.conn.cursor()

    def __del__(self):
        self.cursor.close()
        self.conn.close()

    def execute_sql(self,sql):
        self.cursor.execute(sql) # 这里直接使用cursor
        for temp in self.cursor.fetchall():
            print(temp)

    @ staticmethod
    def print_menu(): # 这个对象不需要实例对象 不需要类对象 是一个静态方法 要去掉self 转为静态方法
        print("--------京东---------")
        print("1.所有的商品")
        print("2.所有的商品分类")
        print("3.所有的商品品牌分类")
        print("4.添加一个商品分类")
        print("5.根据一个名字查询商品")
        return input("请输入功能对应的序号：")

    def show_all_items(self):
        '''显示所有的商品'''
        sql = 'select * from goods;'
        self.execute_sql(sql)

    def show_cates(self):
        '''显示所有的商品分类'''
        sql = 'select name from goods_cates;'
        self.execute_sql(sql)

    def show_brands(self):
        '''显示所有的商品品牌分类'''
        sql = 'select name from goods_brands;'
        self.execute_sql(sql)

    def add_brands(self):
        brand_name = input("请输入品牌的名字……")
        sql = f'insert into goods_brands (name) values("{brand_name}");'
        # self.execute_sql(sql) # 上面遍历使用的激活方法这里不再适用了
        self.cursor.execute(sql)
        self.conn.commit()

    def get_info_by_name(self): # φ(゜▽゜*)♪
        find_name = input("请输入要查询的商品的名字")
        # sql = f'''select * from goods where name="{find_name}";'''
        # " or 1=1 or "1
        # select * from goods where name="" or 1=1 or "1";
        # print(f'------------>{sql}<------------')
        # self.execute_sql(sql)
        # 就了解一下，这玩意一不小心可能就进局子了
        sql = f'select * from goods where name =%s'
        self.cursor.execute(sql,[find_name]) # 让execute函数自己去调用这个输入内容
        print(self.cursor.fetchall())

    def run(self):
        while True:
            ob = self.print_menu() # 实例对象可以调用任何方法：实例方法 类方法 静态方法 类对象只能调用类方法或者静态方法
            if ob == '1':
                # 查询所有的商品 这里的框架搭好了就可以开始写代码，注意这里最好在一个类下面就将所有相关的事情做完，让main函数封装的更加漂亮
                self.show_all_items() # 这里先直接写上需要的函数名 方法名 然后再去根据需要去创建
            elif ob == '2':
                # 查询分类
                self.show_cates()
            elif ob == '3':
                # 查询品牌
                self.show_brands()
            elif ob == '4':
                # 添加品牌分类
                self.add_brands()
            elif ob == '5':
                # 根据名字查询商品
                self.get_info_by_name()
            else:
                print("输入有误，请重新输入...")

def main(): # 实例对象 main函数是封装函数，这个封装函数越简单越好
    # 1.创建一个京东商城对象
    jd = JD()
    # 2.调用这个对象的run方法，让其运行
    jd.run()

if __name__ == '__main__':
    main()
```