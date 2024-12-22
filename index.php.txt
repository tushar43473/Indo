<?php

require_once './Models/developer.class.php';
require_once './Models/game.class.php';
require_once './Models/platform.class.php';




$databaseHandler = new PDO('mysql:host=localhost;dbname=videogames', 'root', 'root');
$databaseHandler->setAttribute(PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION);
$databaseHandler->setAttribute(PDO::ATTR_EMULATE_PREPARES, false);


$statement = $databaseHandler->query('SELECT * FROM `game` LIMIT 50');

// $games = $statement->fetchAll();

$devs = fetchAllDeveloper();
$games = fetchAllGames();
$platForms = fetchAllPlatform();


if (
    isset($_GET['title'])
    && isset($_GET['link']) && filter_var($_GET['link'], FILTER_VALIDATE_URL, )
    && isset($_GET['release_date'])
    && isset($_GET['developer'])
    && isset($_GET['platform'])
) {
   
    $statement = $databaseHandler->prepare('
    INSERT INTO `game` (
        `title`,
        `release_date`,
        `link`,
        `developer_id`,
        `platform_id`
    )
    VALUES (
        :title,
        :release_date,
        :link,
        :developer_id,
        :platform_id
    )
    ');

    $statement->execute([
    ':title' => $_GET['title'],
    ':release_date' => $_GET['release_date'],
    ':link' => $_GET['link'],
    ':developer_id' => $_GET['developer'],
    ':platform_id' => $_GET['platform'],
]);   
 
    
}

//  var_dump($_POST); die;
// var_dump($_GET); die;
// var_dump(empty($_POST['title'])); die; 

$first = isset($_GET['title']);

// var_dump($first); die;

$notCorrect = 
    empty($_GET['title']) 
    && empty($_GET['link']) 
    && empty($_GET['release_date']);



if(
    isset($_POST['id'])
    && isset($_POST['title'])
    && isset($_POST['link']) && filter_var($_POST['link'], FILTER_VALIDATE_URL, )
    && isset($_POST['release_date'])
    && isset($_POST['developer'])
    && isset($_POST['platform'])

) 
  {

            $statement = $databaseHandler->prepare('
                UPDATE `game`
                SET
                `title` = :title,
                `release_date` = :release_date,
                `link` = :link,
                `developer_id` = :developer_id,
                `platform_id` = :platform_id
                WHERE `id` = :id;
            ');
        $statement->execute([
            ':id' => $_POST['id'],
            ':title' => $_POST['title'],
            ':release_date' => $_POST['release_date'],
            ':link' => $_POST['link'],
            ':developer_id' => $_POST['developer'],
            ':platform_id' => $_POST['platform'],
        ]);
    }


if (isset($_POST['delete'])) {
 $databaseHandler->exec('DELETE FROM `game` WHERE `id` = ' . $_POST['delete']);
}



// var_dump($game[$_GET['id']]['title'] ); die;
// var_dump($devs); die;
// var_dump($games); die;
// var_dump($platForm); die;
// var_dump($_GET['release_date']); die;




?>


<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Video games php</title>
    <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.4.1/css/bootstrap.min.css" integrity="sha384-Vkoo8x4CGsO3+Hhxv8T/Q5PaXtkKtu6ug5TOeNV6gBiFeWPGFN9MuhOf23Q9Ifjh" crossorigin="anonymous">
    <link href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/5.12.1/css/all.min.css" rel="stylesheet" />
<body>
    <div class="container">
        <div class="card text-center">
            <img src="images/data-original.jpg" class="card-img-top" alt="Retro gaming banner">
            <div class="card-header">
                <h1 class="mt-4 mb-4">My beautiful video games</h1>
            </div>

            <?php if ($first): ?>
            <?php if ($notCorrect): ?>
                <!-- Affiche une alerte de succès -->
                <div id="answer-result" class="alert alert-danger">
                <i class="fas fa-thumbs-down"></i> Champs vide !!!
                </div>
            <?php else: ?>
                <!-- Affiche une alerte d'erreur -->
                <div id="answer-result" class="alert alert-success">
                <i class="fas fa-thumbs-up"></i> Jeu crée ! <strong>
                </strong>
                </div>
            <?php endif; ?>
            <?php endif; ?>






            <table class="table table-striped">
                <thead>
                    <tr>
                        <th scope="col"># <i class="fas fa-sort-down"></i></th>
                        <th scope="col">Title <i class="fas fa-sort-down"></i></th>
                        <th scope="col">Release date <i class="fas fa-sort-down"></i></th>
                        <th scope="col">Developer <i class="fas fa-sort-down"></i></th>
                        <th scope="col">Platform <i class="fas fa-sort-down"></i></th>
                        <th>Edit</th>
                        <th>Delete</th>
                    </tr>
                </thead>
                <tbody>

                <?php foreach ($games as $game): ?>

                    <tr>
                        <th scope="row"><?= $game->getId() ?></th>
                        <td>
                            <a href="<?= $game->getLink() ?>"><?= $game->getTitle() ?></a>
                        </td>
                        <td><?= $game->getRelease_date() ?></td>
                        
                            <td>
                                <a href="<?= $game->getDeveloper()->getLink() ?>"><?= $game->getDeveloper()->getName() ?></a>
                            </td>
                            <td>
                                <a href="<?= $game->getPlatform()->getLink() ?>"><?= $game->getPlatform()->getName() ?></a>
                            </td>
                            <td>

                            <form action="/">
                                <input type="hidden" name="id" value="<?= $game->getId() ?>" />
                                <button type="submit" class="btn btn-primary btn-sm">
                                <i class="fas fa-edit"></i>
                                </button>
                            </form>

                        </td>
                        <td>

                        <form method="post" action="/">
                            <input type="hidden" name="delete" value="<?= $game->getId() ?>" />
                            <button type="submit" class="btn btn-danger btn-sm">
                                <i class="fas fa-trash-alt"></i>
                            </button>
                            </form>


                        </td>
                    </tr>

                <?php endforeach; ?>

                    <form method="get">
                        <tr>
                            <th scope="row"></th>
                            <td>
                                <input type="text" name="title" placeholder="Title" />
    
                                <br />
                                <input type="text" name="link" placeholder="External link" />
                            </td>
                            <td>
                                <input type="date" name="release_date" />
                            </td>

                          
                            <td>  
                     
                                <select name="developer">           
                                    <?php foreach ($devs as $dev): ?>
                                        <option value="<?= $dev->getId() ?>"><?= $dev->getName() ?></option>
                                    <?php endforeach; ?>
                                </select>    
                                
                            </td>
                            <td>
                                <select name="platform">
                                <?php foreach ($platForms as $platForm): ?>
                                    <option value="<?= $platForm->getId() ?>"><?= $platForm->getName() ?></option>
                                <?php endforeach; ?>
                                </select>
                            </td>
                            <td>
                        

                                <button type="submit" class="btn btn-success btn-sm">
                                    <i class="fas fa-plus"></i>
                                </button>
                            </td>
                            <td></td>
                        </tr>
                    </form>


                    <?php if(isset($_GET['id'])): ?>
                        <form method="post" action="/">
                            <input type="hidden" name="id" value="<?= $_GET['id'] ?>" />

                            <tr>
                            <th scope="row"><?= $_GET['id'] ?></th>
                                <td>
                                
                                <input value="" type="text" name="title" />
                                <br />
                                <input value="" type="text" name="link"/>
                                </td>
                                <td>
                                    <input type="date" name="release_date" />
                                </td>
                                                            
                                <td>  
                                    <select name="developer">           
                                        <?php foreach ($devs as $dev): ?>
                                            <option value="<?= $dev->getId() ?>"><?= $dev->getName() ?></option>
                                        <?php endforeach; ?>
                                    </select>    
                                    
                                </td>
                                <td>
                                    <select name="platform">
                                    <?php foreach ($platForms as $platForm): ?>
                                        <option value="<?= $platForm->getId() ?>"><?= $platForm->getName() ?></option>
                                    <?php endforeach; ?>
                                    </select>
                                </td>
                                <td>
                            

                                    <button type="submit" class="btn btn-success btn-sm">
                                        <i class="fas fa-plus"></i>
                                    </button>
                                </td>
                                <td></td>
                            </tr>
                        </form>
                    <?php endif;?>



                </tbody>
            </table>
            <div class="card-body">
                <p class="card-text">This interface lets you sort and organize your video games!</p>
                <p class="card-text">Let us know what you think and give us some love! 🥰</p>
            </div>
            <div class="card-footer text-muted">
                Created by <a href="https://github.com/M2i-DWWM-0920-Lyon-AURA">DWWM Lyon</a> &copy; 2020
            </div>
        </div>
    </div>
</body>
</html>