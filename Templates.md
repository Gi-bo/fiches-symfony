# Les templates dans Symfony

On a vu comment récupérer des données dans les contrôleurs, grâce aux repositories et Doctrine. Il s'agit maintenant d'exploiter ces données dans les templates où elles sont envoyées...

# exemple 1
Les données arrivents sous forme d'objet ou tableau d'objets (collection). Dans le premier cas, la récupération est plutôt simple : il suffira d'appeler l'objet par le no, utilisé dans le contrôleur, suivi d'un point et de la propriété voulue. Par exemple pour un objet ```post``` qui est l'instance de la classe ```Post```, dont on voudrait récupérer le nom, contenu dans la propriété ```name```, il faudra taper : ```post.name```.

Les choses se compliquent dans le cas où l'on récupère plusieurs objets dans un tableau. Dans ce cas, il faudra opérer une boucle sur ce tableau et traiter chaque élément de ce tableau dans la boucle. Pour revenir sur l'exemple précédent, supposons que nous avons récupérer des instances de la classe ```Post``` dans la variable ```postList```, il faudra créer une boucle dans le template avec l'instructuction Twig ```for```.
```
{% for post in postList %}
<div>
    <h1>{{ post.title }}</h1>
    <p>{{ post.content }}</p>
    <p>{{ post.author }} - {{ post.published_at }}</p>
</div>
{% endfor %}
```
Cet extrait de code permettra d'afficher, pour chaque 'post' contenu dans ```postList```, dans une balise ```div```, le titre (```h1```), le contenu (```p```) ainsi que l'auteur et la date de publication (```p```) correspondant respectivement aux propriétés ```title```, ```content```, ```author``` et ```published_at```.

# exemple 2

Prenons maintenant l'exemple où l'on récupère une liste de catégories que l'on veut utiliser pour afficher de façon dynamique dans un menu. La variable contenant les catégorie s'appelera ```categoryList```, sera l'instance de la classe ```Category``` et possèdera la propriété ```libelle```. La route pour affichera les 'post' d'une catégorie sera de la forme suivante :
```
/**
* @Route("/product-list-bycategory/{id}", name="product_list_bycategory")
*/
```
On va donc utiliser la variable ```productList``` pour créer une ```navbar``` avec une boucle en Twig :

```
<navbar>
{% for category in categoryList %}
<a href="lien vers la page de la catégorie">{{ category.libelle }}</a>
{% endfor %}
</navbar>
```
Il reste à préciser le lien vers la page voulue, à implémenter à l'intérieur de l'attribut ```href```. Ce lien s'écrira de la manière suivante, en Twig :
```
<a href="{{ path('product_list_bycategory', {'id':category.id}) }}">
...
```
Pour faire simple : on donne à l'instruction ```path```, 2 paramètres, séparés par une virgule :
- le nom de la route entre quotes ```product_list_bycategory```
- l'```id``` de la catégorie voulue avec ```category.id```. ```id```représente la propriété 'identifiant' de la catégorie en cours.