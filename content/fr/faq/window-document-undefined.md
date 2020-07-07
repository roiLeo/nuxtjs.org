---
title: window ou document non défini ?
description: window ou document non défini avec Nuxt.js ?
group: Développement
groupPosition: 2
position: 1
---

Cette erreur est due au rendu côté serveur. Si vous devez spécifier que vous souhaitez importer une ressource uniquement côté client, vous devez utiliser la variable `process.client`.

Par exemple, dans votre fichier `.vue` :

```js
if (process.client) {
  require('external_library')
}
```