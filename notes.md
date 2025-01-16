# Conclusion

Je n'ai rien de majeur à redire, ou en tout cas aucun problèmes qui sont du niveau attendu, c'est excellent. J'ai quand même mis ci-dessous la liste des réflexions que je me suis faites en regardant le résultat.

20.

# UX

Ce serait sympa si je n’avais pas à re-rentrer mon nom entre chaque partie :D

# Le reste

ligne 151

`http://127.0.0.1:5500/images-pack/`
et
`https://memories-cz.pages.dev/images-pack/`
sont deux liens absolus.
Si tu utilise juste `/images-pack` alors ce sera un lien relatif (parce que ça ne commence pas par `http(s)://`) à la racine du projet (parce que ça commence par un `/`), et donc tout ce que l'on a besoin de garantir pour que ça marche est que le dossier `image-pack` ne change pas de nom ou de place dans le dossier du projet et ça marchera partout sur cloudflare et en local.

//

`return` n'as pas besoin de parenthèses, c'est utile uniquement si tu fais un calcul et que tu veux forcer un certain ordre.

- ✅ `return false`
- ❌ `return (false)`
- ✅ `return speed * (delta + difficulty)`

//

C'est pas bon que `historyGames` inclut des éléments présentationnels et pas exclusivement l'historique des parties. Ce genre de magouille fait que ton code va devoir être soit réecrit pour corriger ça, ou constamment ignorer les deux premiers éléments de cet array pour des raisons qui ne sont pas immédiatement claires quand tu n'as pas tout le code en tête.

//

Si on se refere a la documentation sur MDN un `<article>` est pour du contenu "auto-suffisant" (est-ce qu'on pourrait le transplanter tel quel dans une autre page?) et je pense que les `.article-information-game` ne remplissent pas ce critère.

extrait de MDN:

> The `<article>` HTML element represents a self-contained composition in a document, page, application, or site, which is intended to be independently distributable or reusable (e.g., in syndication). Examples include: a forum post, a magazine or newspaper article, or a blog entry, a product card, a user-submitted comment, an interactive widget or gadget, or any other independent item of content.

https://developer.mozilla.org/en-US/docs/Web/HTML/Element/article

//

Il y a un problème avec cette section. Beaucoup de données sont redondantes (qui existent en plusieurs exemplaires sous différentes formes).

```js
let allShots = 0;
let allTime = 0;
let usedTime = 0;
let remainingTime = 0;
let usedShots = 0;
let remainingShots = 0;
```

Un exemple plus simple de données redondantes serait une base de données dans laquelle on stocke des utilisateurs, chaque utilisateurs ayant une date de naissance et un âge. Pas bon! L'âge est une information incluse dans la date de naissance et ne prend que très très peu de calcul pour retrouver. Donc si on ne stocke que la date de naissance on ne perd rien et on n'as pas a constamment s'assurer de mettre les deux à jour en même temps pour éviter que le système perde les pedales.

Du coup, on pourrait (et devrait) virer `remainingTime` et `remainingShots` ou virer `userTime` et `usedShots`.

Ce qui fait aussi moins de variables à garder en tête.

//

ligne 204 -> 225

Très inefficace de vider/remplir l'historique à chaque fois qu'on montre l’historique.

On pourrait insérer le nouveau score dans la liste a chaque fin de partie et s'arrêter là. Et si jamais on avait besoin de lire l'historique pour calculer des trucs comme "trouver le high-score" par exemple on pourrait s'en tirer avec un `document.querySelectorAll(".history-element")` pour remettre la main sur les infos.

//

ligne 233 -> 244

Ça ne me paraît pas intuitif que `boot()` retourne des éléments html. Au vu de ce qu'il fait et de son nom, son job est de mettre en place toutes les variables et le document de telle sorte que l'on puisse jouer, pas plus.

... ceci dit, ce n'est pas sssiiiiii mauvais, parce que je peux ignorer le return et ça ne casserais rien.

on pourrait juste écrire ça.
```js
console.log("go game !!");
screenPack.innerHTML ='';

boot(); //toute premiere initialisation

document.querySelectorAll(".cards").forEach((i) => {
		i.addEventListener("click", ()=>{clickCards(i)}) //chaque carte est sensible à l'évènement "click", lui faisant executer "retourner()"
})
```

//

ligne 253 -> 257
Pourquoi le faire comme ça avec des noms de variables pas clair au premier coup d'œil?

On pourrait très bien faire
```js
inputName.onblur = () => {
		inputName.classList.add("is-blur")
};               	//quand input est inactif (que l'utilisateur a cliqué ailleurs pour faire autre chose)
inputName.onfocus = () => {
		inputName.classList.remove("is-blur")
};             	//quand l'utilisateur utilise input (la barre de texte est présente dans la zone de texte)
```

ou

```js
inputName.addEventListener("blur",() => {
		inputName.classList.add("is-blur")
});               	//quand input est inactif (que l'utilisateur a cliqué ailleurs pour faire autre chose)
inputName.addEventListener("focus",() => {
		inputName.classList.remove("is-blur")
});             	//quand l'utilisateur utilise input (la barre de texte est présente dans la zone de texte)
```

//

lignes 231 -> 233

Le léger problème avec le fait que check soit une fonction est que tu l'exécute deux fois. On pourrait l'exécuter une fois et stocker sa valeur de retour dans une variable.
(ou alors on n'exécute `check()` qu'une fois, parce que si c'est faux on a l'air de ne rien faire dans `gaming()`)

Au passage, un nom plus descriptif serait important (un check oui mais pour quoi?)

//

ligne 249 -> 281

https://developer.mozilla.org/en-US/docs/Web/HTML/Attributes/placeholder

ceci dit, le temps que t'as passé à refaire ça n'est pas perdu, c'est bon pour c' que t'as 💪😂

//

288 -> 298

Franchement, pourquoi pas. Instinctivement beaucoup de devs, notamment ceux à la fac ou tout juste sortis te diraient que c'est pas bon parce que tu pourrais juste en faire une boucle comme ci-dessous. Mais t'en a tellement peu, le code est tellement clair que ce genre de façon de faire se défend.

```js
const levels = document.querySelectorAll(".levels");

// aussi faisable avec un forEach
for(let i = 0; i < levels.length; i++){
	levels[i].addEventLisntener("change", () => {
    	selectionLevel.id = i;
	});
}
```

Ton cas est bien si on manage des ressources (e.g. ensemble de textures dans un jeu vidéo) et la méthode que j'ai fournie est mieux si on s'attend à devoir changer la quantité de level et qu'on veut surtout s'assurer qu'on exprime d'abord le lien "le N-ième élément correspondra toujours au N-ième niveau".

//

327

Ça me parait bizarre de synchro la redondance des variables dans `check()`, ça n'as pas l'air d'être son job.

//

359

oui, bon, je vais imaginer que cette accolade fermante est sur une nouvelle ligne.

//

lignes 374 et 388

mmmmhhh :/, je me suis dis que je ne retirerais pas de points sur la propreté parce que rien ne m'a agressé quand j'ai lu en diagonale, mais là c'est dur. Non seulement `a` n'est pas  un nom descriptif, mais t'en a deux différents dans la même fonction et qui ont des plages de valeurs différentes.

`i` dans les boucle c'est un cas particulier qui est acceptable parce qu'on est tous sur la même page concernant ce qu'il vaut dire et à quoi il sert.

//

ligne 456 -> 463

les fonctions `find` sont assez mauvaises à faire en boucle parce que leur job est de chercher un truc dans l'array quand on sait pas où il est. Donc là pour chaque insertion de carte on fouille dans l'array `shuffledPack` deux fois pour trouver un index.

On pourrait très bien juste faire une boucle sur `shuffledPack` pour tout insérer

```js
function distributeTheCards(/*rien ou shuffledPack*/){
	for(let i = 0; i < shuffledPack.length, i++){
    	screenPack.insertAdjacentHTML("beforeend",`
            	<article class="cards unknown" id="C${i}" ><p class="image hide"></p></article>
            	<style>
                    	#C${i}>p{background-image: url("${shuffledPack[i][0]}"); background-position-x: center;}
            	</style>
    	`)
	}
}
```

aussi c'est peut être une meilleure idée que `shuffledPack` soit un array d'objets et pas un array d'arrays. Ça nous permettrait d'écrire un truc comme
`shuffledPack[i].imgSource`
plutôt que
`shuffledPack[i][0]`

//

ligne 491

faudrait séparer la logique de défaite et le setup du plateau, c'est pas normal qu'on ai besoin de se rappeler qu'il "faut perdre" pour démarrer une nouvelle partie.