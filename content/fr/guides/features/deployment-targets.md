---
title: Cibles de déploiement
description: Cibles de déploiement
position: 2
category: features
---

## Hébergement statique

With static hosting, hosting where no server is needed, you can choose to either host a single page application or a multiple page application, also known as static sties. With static hosting no server is needed, meaning your SPA or static site can be hosted on any serverless hosting or CDN for free. For static hosting the target of `static` needs to be added to your `nuxt.config` file.

Avec de l'hébergement statique, vous n'avez pas besoin de serveur, vous pouvez choisir d'héberger une SPA ou une MPA (multiple page application), en somme: des sites statiques. Ce qui veut dire que vous pouvez envoyer le tout sur un hébergement serverless ou un CDN et ce, de manière gratuite. Pour de l'hébergement statique, la propriété `target` doit être mise sur `static` dans le fichier `nuxt.config`.

```js{}[nuxt.config.js]
export default {
  target: 'static' // la valeur par défaut est 'server'
}
```

### SPA

Les Single Page Applications (SPA) sont des pages qui sont render seulement du côté client sans le besoin d'un serveur. Pour déployer une SPA, il faut mettre [le `mode` à `spa`](/guides/features/rendering-modes#spa) et ensuite utiliser la commande `build` pour générer votre application.

```js{}[nuxt.config.js]
export default {
  target: 'static' // la valeur par défaut est 'server'
  mode: 'spa'
}
```

### Sites Statiques

Comme Nuxt.js marche aussi en tant que générateur de sites statiques, you pouvez générer votre application en tant que tel. Générer votre application Nuxt.js et bénéficier de tous les avantages d'une application universelle sans avoir besoin d'un serveur. La commande nuxt `generate` va s'occuper de générer l'HTML pour chacune de vos routes et mettre le tout dans le répertoire `dist`.

Globalement, chaque fichier `.vue` qui sera placé à l'intérieur du répertoire `pages` sera généré en tant que page HTML statique. Cela va améliorer la performance, la SEO (référencement naturel) ainsi que permettre un meilleur support en mode hors-ligne.

<base-alert type="info">

Les sites statiques fonctionnent avec le mode par défaut: [le mode universel](https://nuxtjs.org/guides/features/rendering-modes#universal)

</base-alert>

**Utiliser la commande `nuxt dev` avec `static` comme cible de déploiement peut améliorer l'expérience développeur**

- Enlève `req` & `res` du `context`
- Solution de secours sur le rendu côté client dans le cas d'une 404, d'erreurs et de redirections [voir les solutions de secours d'une SPA](./guides/concepts/static-site-generation#spa-fallback)
- `$route.query` sera toujours égal à `{}` sur le render côté serveur
- `process.static` est vrai.

<base-alert type="info">

Nous mettons de plus `process.target` à disposition pour les auteurs de modules qui souhaiteraient ajouter un comportement particulier en fonction de l'utilisateur visé.

</base-alert>

## Hébergement avec serveur

L'hébergement avec serveur requiert un serveur et a pour but d'être utilisé dans le cas d'applications SSR. Le rendu côté serveur aussi connu sous le nom de SSR, signifie que votre page est générée sur le serveur à chaque fois qu'un utilisateur en fait la requête. Quand l'utilisateur ouvre une de vos pages, son navigateur va envoyer une requête au serveur pour lui demander cette page. Une fois render sur le serveur, la page est renvoyée au navigateur avec tout le contenu.

Pour de l'hébergement avec serveur, la propriété `target` doit être mise à `server` (valeur par défaut). On utilise la commande `build` pour générer l'application.

```js{}[nuxt.config.js]
export default {
  target: 'server'
}
```

<base-alert type="info">

Les SSR fonctionnent avec le mode par défaut: [le mode universel](https://nuxtjs.org/guides/features/rendering-modes#universal)

</base-alert>
