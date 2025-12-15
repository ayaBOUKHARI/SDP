# Question 1 : Explication de type (1-1)

## Formulation mathématique du programme linéaire

### Données d'entrée
- **x**, **y** : vecteurs de notes des deux candidats (dimension n)
- **w** : vecteur de poids (dimension n)
- **contributions[i]** = w[i] × (x[i] - y[i])

### Classification des critères
- **pros(x,y)** = {i | contributions[i] > 0} : critères favorables à x
- **cons(x,y)** = {i | contributions[i] < 0} : critères défavorables à x
- **neutral(x,y)** = {i | contributions[i] = 0} : critères neutres

### Variables de décision
- **z[p,c]** ∈ {0,1} pour tout p ∈ pros(x,y), c ∈ cons(x,y)
  - z[p,c] = 1 si on utilise le trade-off (p,c) dans l'explication
  - z[p,c] = 0 sinon

### Contraintes

**C1 : Couverture de tous les critères défavorables**
```
∀c ∈ cons(x,y) : Σ(p∈pros) z[p,c] = 1
```
Chaque critère défavorable doit être couvert exactement une fois.

**C2 : Chaque critère favorable utilisé au plus une fois**
```
∀p ∈ pros(x,y) : Σ(c∈cons) z[p,c] ≤ 1
```
Chaque critère favorable ne peut être utilisé que dans au plus un trade-off.

**C3 : Validité des trade-offs**
```
∀(p,c) : contributions[p] + contributions[c] ≥ ε × z[p,c]
```
Si z[p,c] = 1, alors le trade-off doit avoir une contribution positive.
(ε = 0.01 pour éviter les problèmes numériques)

### Fonction objectif
```
min Σ(p∈pros, c∈cons) z[p,c]
```
Minimiser le nombre de trade-offs (longueur de l'explication).

### Interprétation du résultat

- **OPTIMAL** : Une explication de type (1-1) existe
  - Les paires (p,c) avec z[p,c] = 1 constituent l'explication

- **INFEASIBLE** : Aucune explication de type (1-1) n'existe
  - Cela arrive typiquement quand |pros(x,y)| < |cons(x,y)|
  - Ou quand les contributions positives ne peuvent pas "compenser" les négatives

## Exemple : x vs y

### Données
```
Critères:  A    B    C    D    E    F    G
x:        85   81   71   69   75   81   88
y:        81   81   75   63   67   88   95
weights:   8    7    7    6    6    5    6
```

### Contributions
```
A: +32.0  (pour x)
B:   0.0  (neutre)
C: -28.0  (contre x)
D: +36.0  (pour x)
E: +48.0  (pour x)
F: -35.0  (contre x)
G: -42.0  (contre x)
```

### Classification
- pros(x,y) = {A, D, E}
- cons(x,y) = {C, F, G}
- neutral(x,y) = {B}

### Explication optimale
1. (A, C) : +32 - 28 = +4 > 0
2. (D, F) : +36 - 35 = +1 > 0
3. (E, G) : +48 - 42 = +6 > 0

**Explication en langage naturel :**
x est meilleur que y car :
- l'avantage de x sur y en Anatomie pèse plus fort que son désavantage en Chirurgie
- l'avantage de x en Diagnostic pèse plus fort que le désavantage en Forensic pathology
- l'avantage de x en Epidemiologie pèse plus fort que le désavantage en Génétique

## Exemple : w vs w' (pas d'explication 1-1)

### Données
```
Critères:  A    B    C    D    E    F    G
w:        79   69   78   76   67   84   79
w':       57   76   81   76   82   86   77
weights:   8    7    7    6    6    5    6
```

### Contributions
```
A: +176.0  (pour w)
B: -49.0   (contre w)
C: -21.0   (contre w)
D:   0.0   (neutre)
E: -90.0   (contre w)
F: -10.0   (contre w)
G: +12.0   (pour w)
```

### Classification
- pros(w, w') = {A, G}  (seulement 2 critères)
- cons(w, w') = {B, C, E, F}  (4 critères)

### Raison de l'infaisabilité
Il n'existe pas d'explication de type (1-1) car :
- On a 4 critères dans cons(w, w')
- On a seulement 2 critères dans pros(w, w')
- Un trade-off (1-1) couvre exactement 1 critère de cons
- Il faudrait donc 4 trade-offs, mais on n'a que 2 critères disponibles dans pros

**De plus**, même si on utilise A et G :
- A seul ne peut pas compenser plusieurs critères de cons
- G (+12) ne peut compenser que C (-21) mais pas les autres

→ Le modèle est **infaisable**.

## Configuration de la licence Gurobi

Le code nécessite une licence Gurobi valide. Si vous voyez l'erreur "License has expired" :

### Option 1 : Licence académique gratuite
1. Créez un compte sur https://www.gurobi.com avec votre email académique
2. Téléchargez une licence académique gratuite
3. Installez la licence avec : `grbgetkey VOTRE_CLE`

### Option 2 : Utiliser un autre solveur
Si vous n'avez pas accès à Gurobi, vous pouvez modifier le code pour utiliser :
- **PuLP** avec le solveur CBC (open-source)
- **Google OR-Tools** (open-source)
- **CVXPY** avec différents backends


