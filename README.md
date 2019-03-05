From this Gatsby starter: https://www.gatsbyjs.org/starters/alxshelepenok/gatsby-starter-lumen/

## Developing
If using `npm` replace `yarn` below with `npm`

```
$ yarn install
$ yarn run develop
```

## Deploying
Note that the `development` branch is used for actually updating/creating posts and the deploy script runs on `master` branch.

Initially ran:
```
$ git checkout -b development
$ git push --set-upstream origin development
```

Once changes are made then run `yarn run deploy` or `npm run deploy`.