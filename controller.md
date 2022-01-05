## Make Easy Crud With Node.Express

### Intro :
Les controllers pointent vers des URL que vous definissez vous même.
Exemple, je souhaite créer un point d'entrée sur mon serveur à partir de l'url `/api` 

Je vais donc créer un controller spécifique pour toutes les urls qui commenceront par `/api`.

Ainsi, à partir de là je vais pouvoir définir un certain nombre d'entrées depuis cette url :
Exemple, je souhaite que lorque un client requête mon serveur en GET sur /api il obtienne un message "Welcome !" :
```
router.get("/", (req, res) => {
    res.send('Welcome').status(200); 
});
```

### CRUD :

[**Create**] doit être une route en méthode **POST**
```
router.post("/", (req, res) => {
    res.send('Welcome').status(200); 
});
```

Si vous utilisez une architecture MVC alors le controller devra importer le model de l'entité qu'il manage.
```
router.post("/", async (req, res) => {
// Récupérer depuis le body de la request les valeurs des champs de l'objet à créer en BDD
  const objectToCreate = [
    req.body.attr1,
    req.body.attr2,
    ...,
  ];
// Try to create
  try {
    // Create new object depuis le model 'Entity' et récupère son id
    const lastInsertId = await Entity.createNew(objectToCreate);
    // Récupérer l'ensemble de l'objet grace à son id et au model
    const newObject = await Album.findById(lastInsertId);
    // Retourner status 201 create + le nouvel objet
    res.status(201).json(newObject);
  } catch (err) {
// Si erreur lors de la création 'catch' error 500
    res.status(500).json({ message: err.message });
  }
});
```

Si vous n'utilisez pas le MVC, alors vous pouvez importer directement la connection et écrire la requête SQL dans la méthode.

```
router.post("/", (req, res) => {
// Récupérer depuis le body de la request les valeurs des champs de l'objet à créer en BDD
  const objectToCreate = [
    req.body.attr1,
    req.body.attr2,
    ...,
  ];
    dbConnect.query('INSERT INTO table (attr1, attr2, ...) VALUES (?, ?, ...)', objectToCreate, (error, results) => {
        if (err) res.send(error).status(500);
        else {
                const newId = results.insertId;
                dbConnect.query('SELECT * FROM album WHERE id = ?', newId, (err, result) => {
                if (err) res.send(err).status(500);
                else res.json(result[0]).status(201);
            })
        }
    })
});
```
[**Read**] doit être une route en méthode **GET**

> Avec MVC :
> Read All
```
router.get("/", async (req, res) => {
  try {
    const objects = await Entity.findAll();
    res.json(objects).status(200);;
  } catch (error) {
    res.status(500).json({ message: error.message });
  }
});
```

> Sans MVC : 
> Read All
```
router.get("/", async (req, res) => {
  dbConnect.query('SELECT * FROM table', (err, results) => {
    if (err) res.send(err).status(500);
        else res.json(results).status(200);
    })
});
```

> Avec MVC :
> Read One
```
router.get("/", async (req, res) => {
  try {
    const object = await Entity.findOnById(req.params.id);
    res.json(object[0]).status(200);
  } catch (error) {
    res.status(500).json({ message: error.message });
  }
});
```

> Sans MVC : 
> Read All
```
router.get("/", async (req, res) => {
  dbConnect.query('SELECT * FROM table WHERE id = ?', req.params.id (err, results) => {
    if (err) res.send(err).status(500);
        else res.json(results[0]).status(200);
    })
});
```

[**Update**] doit être une route en méthode **PUT** ou **POST**

> Avec MVC :

```
router.put('/:id', async (req, res) => {
    try {
        const objectUpdate = await Object.updateTitle(req.body.attr, req.params.id);
        if (objectUpdate) res.status(204);
        else res.status(422).json({ message: 'message d'erreur' });
    } catch (err) {
        res.status(500).json({ message: err.message });
    }
});
```

> Sans MVC :

```
router.put('/:id', async (req, res) => {
    dbConnect.query('UPDATE table SET attr = ? WHERE id = ?', [attr, id], (err, result) => {
        if (err) res.send(err).status(500)
        else res.status(200).json({message : "Object updated"})
    })
});
```

[**Delete**] doit être une route en méthode **DELETE**

> Avec MVC :

```
router.delete('/:id', async (req, res) => {
    const id = req.params.id;
    try {
        const result = await Entity.deleteId(id);
        result ? res.json({message : `ObjectId ${id} has been deleted !`}).status(200) : res.status(404).json({ message: 'Object not found' });
    } catch (error) {
        res.status(500).json({ message: error.message });
    }
})
```

> Sans MVC :

```
router.delete('/:id', async (req, res) => {
    const id = req.params.id;
    dbConnect.query('DELETE FROM table WHERE id = ?', id, (err, result) => {
        if (err) res.send(err).status(500);
        else result ? res.json({message : `ObjectId ${id} has been deleted !`}).status(200) : res.status(404).json({ message: 'Object not found' });
    })
})
```

### Attention tout le code présent doit être réajuster à vos besoins.