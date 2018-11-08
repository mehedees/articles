SQLAlchemy
---


* **Version Check**
```python
    >>>import sqlalchemy
    >>>sqlalchemy.__version__ 
```
* **Connecting**
  For this tutorial we will use an in-memory-only SQLite database. To connect we use create_engine():
```python
    >>>from sqlalchemy import create_engine
    >>>engine = create_engine('sqlite:///:memory:', echo=True)
```
* **Declare a Mapping**
```python
    >>>from sqlalchemy.ext.declarative import declarative_base
    >>>Base = declarative_base()
```
```python
    >>> from sqlalchemy import Column, Integer, String
    >>> class User(Base):
    ...     __tablename__ = 'users'
    ...
    ...     id = Column(Integer, primary_key=True)
    ...     name = Column(String)
    ...     fullname = Column(String)
    ...     password = Column(String)
    ...
    ...     def __repr__(self):
    ...        return "<User(name='%s', fullname='%s', password='%s')>" % (
    ...                             self.name, self.fullname, self.password)
```
* **Create a Schema**
```python
    >>> User.__table__ 
    Table('users', MetaData(bind=None),
                Column('id', Integer(), table=<users>, primary_key=True, nullable=False),
                Column('name', String(), table=<users>),
                Column('fullname', String(), table=<users>),
                Column('password', String(), table=<users>), schema=None)
```
The Table object is a member of a larger collection known as MetaData. When using Declarative, this object is available using the .metadata attribute of our declarative base class.
The MetaData is a registry which includes the ability to emit a limited set of schema generation commands to the database. As our SQLite database does not actually have a users table present, we can use MetaData to issue CREATE TABLE statements to the database for all tables that don’t yet exist. Below, we call the MetaData.create_all() method, passing in our Engine as a source of database connectivity. We will see that special commands are first emitted to check for the presence of the users table, and following that the actual CREATE TABLE statement:
```python
    >>> Base.metadata.create_all(engine) 
    PRAGMA table_info("users")
    ()
    CREATE TABLE users (
        id INTEGER NOT NULL,
        name VARCHAR,
        fullname VARCHAR,
        password VARCHAR,
        PRIMARY KEY (id)
    )
    ()
    COMMIT
```
*   **Minimal Table Descriptions vs. Full Descriptions**
    Users familiar with the syntax of CREATE TABLE may notice that the VARCHAR columns were generated without a length; on SQLite and Postgresql, this is a valid datatype, but on others, it’s not allowed. So if running this tutorial on one of those databases, and you wish to use SQLAlchemy to issue CREATE TABLE, a “length” may be provided to the String type as below:
    `Column(String(50))`
    The length field on String, as well as similar precision/scale fields available on Integer, Numeric, etc. are not referenced by SQLAlchemy other than when creating tables.
    Additionally, Firebird and Oracle require sequences to generate new primary key identifiers, and SQLAlchemy doesn’t generate or assume these without being instructed. For that, you use the Sequence construct:
    ```python
        from sqlalchemy import Sequence
        Column(Integer, Sequence('user_id_seq'), primary_key=True)
    ```









