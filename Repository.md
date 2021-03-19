# Les repositories dans Symfony

Nous avons pu remarqué que lors de la création d'une entity avec l'instruction ```php bin/console make:entity``` un autre fichier était créé dans le dossier Repository. Ce fichier porte le no, de l'entity créée associé au terme "repository". Par exemple, si l'entity créée se nomme ```Product.php``` alors ```ProductRepository.php``` apparaîtra en même temps.

Voici à quoi ressemble ce fichier :
```
<?php

namespace App\Repository;

use App\Entity\Product;
use Doctrine\Bundle\DoctrineBundle\Repository\ServiceEntityRepository;
use Doctrine\Persistence\ManagerRegistry;

/**
 * @method Product|null find($id, $lockMode = null, $lockVersion = null)
 * @method Product|null findOneBy(array $criteria, array $orderBy = null)
 * @method Product[]    findAll()
 * @method Product[]    findBy(array $criteria, array $orderBy = null, $limit = null, $offset = null)
 */
class ProductRepository extends ServiceEntityRepository
{
    public function __construct(ManagerRegistry $registry)
    {
        parent::__construct($registry, Product::class);
    }

    // /**
    //  * @return Product[] Returns an array of Product objects
    //  */
    /*
    public function findByExampleField($value)
    {
        return $this->createQueryBuilder('p')
            ->andWhere('p.exampleField = :val')
            ->setParameter('val', $value)
            ->orderBy('p.id', 'ASC')
            ->setMaxResults(10)
            ->getQuery()
            ->getResult()
        ;
    }
    */

    /*
    public function findOneBySomeField($value): ?Product
    {
        return $this->createQueryBuilder('p')
            ->andWhere('p.exampleField = :val')
            ->setParameter('val', $value)
            ->getQuery()
            ->getOneOrNullResult()
        ;
    }
    */
}
```

Pour simplifier les choses, ce fichier contient les méthodes nécessaires pour récupérer les instances de la classe/entity correspondante. 4 méthodes apparaissent ici :
```
/**
 * @method Product|null find($id, $lockMode = null, $lockVersion = null)
 * @method Product|null findOneBy(array $criteria, array $orderBy = null)
 * @method Product[]    findAll()
 * @method Product[]    findBy(array $criteria, array $orderBy = null, $limit = null, $offset = null)
 */
```
Elles sont implémentées sous forme d'annotation :

```find($id)``` permettra de retrouver une instance avec son ```id```.

```findOneBy(array $criteria, array $orderBy = null)``` permettre de retrouver une instance avec un critère autre que ```id```.

```findAll()``` retournera toutes les instances de l'entity.

```findBy(array $criteria, array $orderBy = null, $limit = null, $offset = null)``` retrounera toutes les instances correspondantes au critère spécifié en paramètre.

Nous allons voir quelques exemples d'application.

# Exemple 1
Je veux récupérer toutes les instances de la classe ```Post```, et les transmettre via un contrôleur ```HomeController``` au template ```home.html.twig```.

```
<?php

namespace App\Controller;

use App\Entity\Post;
use App\Repository\PostRepository;
use Symfony\Bundle\FrameworkBundle\Controller\AbstractController;
use Symfony\Component\HttpFoundation\Response;
use Symfony\Component\Routing\Annotation\Route;

class HomeController extends AbstractController
{
    /**
     * @Route("/post-list", name="post_list")
     */
    public function list(PostRepository $postRepository): Response
    {
        // on récupère tous les posts dans une variable $postList
        $postList = $postRepository->findAll(['name'=>'ASC']);
        return $this->render('home.html.twig', [
            // on envoie le tableau qui contient les données en associant la variable au paramètre 'postList' de la fonction render().
            'postList' => $postList,
        ]);
    }
```
On utilise ```PostRepository``` en paramètre de la fonction qui va gérer l'affichage du template. On créé la variable ```$postList``` qui va contenir à l'intérieur d'un tableau toutes les instances de l'entity ```Post```, c'est à dire tous les "posts" contenus dans la bdd. Pour récupérer tout cela, on utilise la méthode ```findAll()``` de la classe ```PostRepository```.

Il faut encore envoyer tout cela dans le template pour pouvoir l'utiliser. On va donc en voyer cela en le mettant en paramètre de la fonction ```render()```. C'est l'élément ```'postList'``` du tableau, mis en 2ème paramètre de la fonction ```render()```.


# exemple 2
On veut maintenant récupérer les "posts" de la bdd qui correspondent à une catégorie précise. Supposons que cette catégorie se nomme "Informatique" et porte l'```id``` 26.

Il suffit pour cela de remplacer la valeur transmise à la variable ```$postList```, grâce à cette instruction :
```
    /**
    * @Route("/product-list-bycategory/{id}", name="product_list_bycategory")
    */
    public function IndexByCategory(PostRepository $postRepository, $id): Response
    {    
        $postList = $postRepository->findBy(['id' => $id],['name'=>'ASC']);
        ...
```
La fonction ```indexByCategory()``` prendra en paramètre :

- ```$postRepository```, pour utiliser la méthode ```findBy()```
- ```$id```, qui contiendra l'```id``` de la catégorie voulue(on pourrait ne pas utiliser ce paramètre et mettre ```'id' => 26``` en "dur")



# Cas d'entity sans repository associé
Dans ce cas, on ne peut pas mettre en paramètre le repository correspondant dans la fonction qui va gérer l'affichage du template...

On va alors implémenter différemment la requête pour récupérer les instances. Par exemple, si PostRepository n'existe pas, on peut écrire :
```
$postList = $this->getDoctrine()->getRepository(Post::class)->findAll(['name' => 'ASC'])
```

sans oublier d'appeler l'entity ```Post```.
