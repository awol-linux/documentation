# Creating a library to interact with a MySQL db

## Dependencys:

1. A mysql db
1. Python 3 
1. pymysql

## Theory

What we need:
1. A Decorator and to handle
    1. Opening/Logon
    2. Executing
    4. commiting or rolling back
    6. Closing

2. A consistent way to interact with our library:
    1. Run a straight query and fetch all results
    2. Run a straight query and return a generator to iterate through the results
    3. Parse a bunch of KV attributes into a query
    4. Parse a query return into a dictionary

## implementaition:
