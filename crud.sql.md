## Make Easy Crud With MySQL

### Create SQL request

#### CREATE
> `INSERT INTO table (attr1, attr2, ...) VALUES (?,?,...)`

Il faudra ensuite passer la valeur des paramètres '?' dans l'ordre et dans un **tableau** ! 
> `const values = ["plop", "plop", ....]`

#### READ
> `SELECT * FROM table`
> `SELECT * FROM table WHERE id = ?`

Ne pas oublier de passer le param id pour '?'

#### UPDATE
> `UPDATE table SET attr = ?, attr2 = ?, [...] WHERE id = '?'`

De la même manière que pour le CREATE, il faudra passer les valeurs des paramètres '?' dans l'ordre et dans un tableau.

#### DELETE
> `DELETE FROM table WHERE id = '?'`

Ne pas oublier de passer le param id pour '?'


