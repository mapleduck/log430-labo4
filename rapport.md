# LOG430 - Rapport du laboratoire 02
ÉTS - LOG430 - Architecture logicielle - Hiver 2026 - Groupe 1

Étudiant: Yanni Haddar
Nom github: mapleduck
repo github: github.com/mapleduck/log430-labo4

## Questions

Compte tenu d'une panne tepmoraire du reseau de l'ecole, jai acocmpli les performance tests localement.

First try on locust run = hard sql crash.


Aussi jai modifie le mauvais ifhciermaaaan


## Déploiement

Pour commencer, voici le résultat du script de test de supplier app:
```
dusty@dusty-laptop:~/SchoolRepos/log430-labo3-mapleduck/scripts$ docker compose up
Attaching to supplier_app-1
supplier_app-1  | 2026-02-12 23:57:49,212 - INFO - Starting periodic calls to http://store_manager:5000/stocks/graphql-query every 10 seconds
supplier_app-1  | 2026-02-12 23:57:49,212 - INFO - Press Ctrl+C to stop
supplier_app-1  | 2026-02-12 23:57:49,213 - INFO - --- Call #1 ---
supplier_app-1  | 2026-02-12 23:57:49,213 - INFO - Calling http://store_manager:5000/stocks/graphql-query (attempt 1/3)
supplier_app-1  | 2026-02-12 23:57:49,219 - INFO - Response: 200 - OK
supplier_app-1  | 2026-02-12 23:57:49,219 - INFO - Response body: {"data":{"product":{"id":1,"name":"Laptop ABC","price":1999.99,"quantity":1991,"sku":"LP12567"}},"errors":null}
supplier_app-1  | ...
supplier_app-1  | 2026-02-12 23:57:49,219 - INFO - Waiting 10 seconds until next call...
Gracefully Stopping... press Ctrl+C again to force
```
Succès.



Déploiement très similaire au dernier labo, il faut encore une fois spécifier les noms des conteneurs car docker leur donne un nom unique et ils ne peuvent donc pas se retrouver entre eux.

Les tests échouaient, puis je me suis rendu compte que lors de ce labo, il avait fallu créer la table stock, elle n'étais pas créé par défaut (ce qui était un des buts du labo). Mais évidemment, il faut faire de même sur la VM, car la VM n'a pas non plus la base de données.

Ceci a donc été ajouté dans le `init.sql`:
```
-- Product stocks 
-- Il faut automatiquement rajouter la table stock pour que le CI fonctionne.
DROP TABLE IF EXISTS stocks;
CREATE TABLE stocks (
    product_id INT PRIMARY KEY,
    quantity INT NOT NULL DEFAULT 0,
    FOREIGN KEY (product_id) REFERENCES products(id) ON DELETE CASCADE
);

-- Mock data
INSERT INTO stocks (product_id, quantity) VALUES
(1, 10),
(2, 50),
(3, 100),
(4, 0);
```

Résultat final, les tests passent le CI via le runner:

![resultat github](./docs/img/Screenshot%20from%202026-02-12%2020-45-31.png)