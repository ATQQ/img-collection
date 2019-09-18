# 数据库表v1.0.0
**Tips**
* NN=NOT NULL
* PK=PrimaryKey
* N=default NULL
* UN=Unique
* AI=Auto Increase
### user_token(令牌)

| name  |    type     | property |   comment    | example |
| :---: | :---------: | :------: | :----------: | :-----: |
|  id   |     int     | PK/NN/AI | 主键自增非空 |    1    |
| token | varchar(64) |    NN    |     令牌     | ABC123  |

### types(分类)
|   name   |    type     | property |   comment    | example |
| :------: | :---------: | :------: | :----------: | :-----: |
|    id    |     int     | PK/NN/AI | 主键自增非空 |    1    |
| token_id |     int     |    NN    |   令牌的id   |    3    |
|   name   | varchar(64) |    NN    |   分类名称   |  苹果   |

### images(图片)
|     name     |     type     | property |   comment    | example  |
| :----------: | :----------: | :------: | :----------: | :------: |
|      id      |     int      | PK/NN/AI | 主键自增非空 |    1     |
|   token_id   |     int      |    NN    |   令牌的id   |    3     |
|   types_id   |     int      |    NN    |    分类id    |   苹果   |
| picture_name | varchar(256) |    NN    |   图片名称   | 苹果.png |


### 通有字段
|     name     |   type   | property |     comment      |  example  |
| :----------: | :------: | :------: | :--------------: | :-------: |
| create_date  | datetime |    NN    |     创建日期     | 123456789 |
| update_date  | datetime |    NN    | 最后一次更新日期 | 123456789 |
| update_times |   int    |    NN    |     修改次数     |     5     |




