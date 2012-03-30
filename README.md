Usage
=====

```python
from sqlalchemy import create_engine, MetaData, Integer
from sqlalchemy.schema import Column, Table
import sqlalchemy.orm as orm

engine = create_engine('postgresql://localhost/test_db')
meta = MetaData()

test_table = Table('test', meta,
    Column('id', Integer(), primary_key=True),
    Column('txt', CIText()))

conn = engine.connect()

meta.bind = conn
meta.drop_all()
meta.create_all()

class TestObj(object):
    def __init__(self, id_, txt):
        self.id = id_
        self.txt = txt

    def __repr__(self):
        return "TestObj(%r, %r)" % (self.id, self.txt)

orm.mapper(TestObj, test_table)
Session = orm.sessionmaker(bind=engine)
ses = Session()

to = TestObj(1, txt='FooFighter')
ses.add(to)
ses.commit()
row = ses.query(TestObj).filter(TestObj.txt == 'foofighter').all()
assert len(row) == 1
print row
ses.close()
```
