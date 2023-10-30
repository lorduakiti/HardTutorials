Instalation Heroku CLI
npm install -g heroku
heroku --version

heroku login
   .. ou
heroku login -i

Setting a buildpack on an application
heroku buildpacks:set heroku/nodejs
node --version
    ... ou 
heroku buildpacks
   {
     "name": "myapp",
     "description": "a really cool app",
     "version": "1.0.0",
     "engines": {
       "node": "12.13.0",
       "npm": "6.x",
       "yarn": "1.x"
     }
   }

heroku config:set NPM_CONFIG_PRODUCTION=true YARN_PRODUCTION=true

git push heroku master

heroku buildpacks:remove heroku/nodejs

Add-ons
heroku addons:create heroku-postgresql

Create app
heroku create myapp --buildpack heroku/python


CLI Plugins
heroku plugins:install heroku-pg-extras
      pg:cache-hit
      pg:index-usage
      pg:locks
      pg:outliers
      pg:calls
      pg:blocking
      pg:total-index-size
      pg:index-size
      pg:table-size
      pg:table-indexes-size
      pg:total-table-size
      pg:unused-indexes
      pg:seq-scans
      pg:long-running-queries
      pg:records-rank
      pg:bloat
      pg:vacuum-stats
      pg:user-connections
      pg:mandelbrot

heroku plugins:install api