# Idées en vrac

Contenu de la présentation :
* de la bagarre
* des anecdotes
* un peu de technique (quand même)

Intro qu'a pas grand chose à voir mais un peu quand même

Ce week-end, j'étais à Paris, et pas seulement pour la bouffe. Ce week-end se tenait les quarts de finale des championnats du monde de League of Legends (les "Worlds" en plus court, pour les initiés). Week-end au cours duquel se sont affrontés les meilleures équipes mondiales, venues de Chine, de Corée, d'Europe évidemment, ainsi que d'Amérique du Nord. Les occidentaux se sont encore une fois pris une branlée, mais c'est pas important pour la suite. Ce qui est important, c'est le format qui permet de se qualifier à cette ô combien prisée phase finale du main stage.

Critique du format précédent

Format championnat classique, phases de poules, 2 premiers se qualifient pour les phases finales, 2 derniers rentrent chez eux, en avion s'ils se sont bien battus, à pied s'ils ont été ridicules, à la nage s'ils sont américains.
Besoin de changement pour renouveler le système (qui dure déjà depuis quelques années).

___
Transition: -> Et comme pour tout ce qui fait en système compétitif sur n'importe quel support, on va faire comme d'habitude, et copier les échecs.

Explication Système Suisse

Originaire d'une compétition ayant eu lieu ... en Suisse, à Zurich. Réfléchi pour permettre d'évaluer équitablement le niveau individuel de chaque personne s'affrontant, il permet de faire s'opposer à chaque nouvelle rencontres, des personnes ayant été équivalent en terme réussite.

*Grosso modo* : Les forts rencontrent des forts, et les nuls rencontrent des nuls.

P'tit schéma pour dire quoiquispasse dans un système suisse

__Après l'explication du système suisse__

Oui, mais signalR dans tout ça ? Le rapport avec la choucroute ? Je vous promets, on y arrive très vite

Je vous ai parlé des Worlds tout à l'heure, où s'affrontent les meilleurs joueurs du monde. Mais la réalité, c'est qu'on est aujourd'hui chez Max, et même si on est pas les meilleurs du monde, on aime bien s'affronter aussi, à l'occasion de petites soirées Lan organisées entre autres par Audren, Alexis, et moi-même.

La fameuse MaxLan, qui se généralement en mai et en novembre, qui s'organisait jusqu'alors par le biais d'un magnifique fichier excel gérant l'inscription, l'organisation des équipes et des poules, le matos, et dont les résultats étaient gérés sur des tableaux blancs dans chaque salle

Au fil des éditions et du temps, et par envie de renouveau, le format a été remis en question. Jusqu'alors sur un système traditionnel (3 poules, 2 premiers de chaque poule en phases finales, repechages entre les 3eme pour le dernier quart de finale), le système suisse a été assez vite évoqué, pour diversifier les affrontements, et éviter les "poules de la mort".

"Problème" : beaucoup d'applis qui permettent de gérer son système suisse soit même (avec inscription des équipes, màj du format petit à petit, etc)

En quoi c'est un problème ? C'est pas fait par un Max, et ça répond SEULEMENT à 85% du besoin. Donc j'ai fais comme tout bon développeur qui a de l'égo

fineilldoitmyself.gif

# Genèse de l'appli

back en .net8, appli web classique, api toute simple, aucune sécu parce que pas besoin
modèle de données ultra simpliste, pas de nécessité de mettre en place de vrai bdd derrière, avec un orm et tout (qui a le temps pour ça ?) donc synchro des données dans un json dans un premier temps, en utilisant une lib qui pompe les mêmes principes qu'un mongo, avec quasi les mêmes signatures de méthodes, comme ça SI JAMAIS Y'A VRAIMENT BESOIN (mais bon ... en vrai ?), on pourra mettre un mongo

front minimaliste rapide et efficace, react nature peinture, avec un peu de tailwind, mais en fait ça m'a pas trop plu

vite fait faire les api qui permettent de jouer avec les données :
- créer le tournoi
- importer les jeux
- ^ les équipes
- les endpoints nécessaires aux joueurs (j'ai gagné, j'ai perdu, prochain matchup) et 
- _________________________ aux admins (prochain round une fois que tout le monde a joué, reset en cas de problème, forcer des résultats)

tester vite fait que ça marche avec swagger, et commencer à faire vite fait une interface pour que ça ressemble à quelque chose

assez vite, j'me dis que ça serait chouette d'avoir les résultats en temps réel

je check vite fait colyseus, parce qu'audren et quentin sont beaucoup trop forts, et ont fait des trucs de fou avec, mais c'est full js. Pas de bol, tout le back est déjà fait, flemme de tout jeter à la poubelle (et flemme de faire du js ? nan ? un peu hein ?)

et puis j'me dis "attend ... ça serait bête de faire autre chose que du .net, ils ont un framework tout fait pour ..."

# SignalR

brève explication de l'utilité de signalR, et de comment ça marche ? 

technologie real time en .net, côté serveur/client (lourd ou web via js/ts), qui switche sur la première techno supportée sur le server (WebSockets, SSE, long-polling, forever frame ...)

bon du coup, avec la mise en place de signalR, ça rend pas mal d'endpoints des apis un peu obsolètes, puisque la plupart des interactions vont passer par ça

raccourci pour raconter l'implémentation, le design incroyable de l'application, et les différents soucis d'algo, fast forward le jour de la lan, l'appli est presque pas totalement complètement terminée, et mon binome de légende, Alexis Delhelle, fait des tests grandeur nature

# ET LA, ça charge 🥵

ce meme de ça charge, j'en bouffe depuis la dernière lan, en mai, c'est un bête loader que j'avais mis à chaque fois qu'une requête partait et qu'on attendait le résultat

et attendu nous avons ...

ça a été globalement une catastrophe de perfs toute la soirée, mais ça fonctionnait quand même, avec de la patience

et toute la soirée, SignalR a été raillé, subissant sa réputation de framework alambiqué et lourdeau

# Le lendemain, lorsque l'orage fut calmé

Je remet les mains dedans, prêt à dérouiller SignalR et lui faire manger ses chaussettes du web mais en cherchant sur le net, on peut pas vraiment dire que ça soit vraiment signalR le fautif, il est même plutôt réglo le type

mais y'a un fautif, que j'avais pas vraiment considéré parce que vraiment, le jeu de données est pas colossal ... mais en vrai, un json qui est tapé dedans en boucle, à chaque appel, par une lib douteuse d'un gars dans sa grotte ? c'est pas plus suspect qu'un framework développé par un géant comme microsoft ? si hein ?

du coup je dézingue la lib, je vire le json, et je met un mongo. 30min, allez 45min avec les installs mongo, et comprendre comment ça marche, et on avait même plus le temps de voir apparaître les loaders ... 

# moralité

double dose de moralité 
- on met en place des choses que parfois, on sait très bien que c'est un peu crado et que ça peut être source de problème, et des fois on les passe à la trappe en se disant que c'est minime -> toujours garder en tête les trucs crados qu'on fait, pour pouvoir revenir facilement dessus
- quand on a eu des difficultés avec un framework, aussi populaire et répandu soit-il, il ne faut pas nécessairement tout lui mettre sur le dos si quelque chose d'AUTRE se passe mal une nouvelle fois dans un contexte ou il est réutilisé

# Mea Culpa SignalR, tu étais brave