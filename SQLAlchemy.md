SQLAlchemy
---
* Version Check
```python
  import sqlalchemy
  sqlalchemy.__version__ 
```
* Connecting
**  For this tutorial we will use an in-memory-only SQLite database. To connect we use create_engine():
```python
  from sqlalchemy import create_engine
  engine = create_engine('sqlite:///:memory:', echo=True)
```
