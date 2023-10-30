(Top-down approach / design-first)
```
git clone https://github.com/swagger-api/swagger-editor.git  swagger-editor-doc
cd swagger-editor-doc
http://localhost/swagger-editor-doc
start .
npm install -g swagger
npm install --save swagger-ui
npm install --save swagger-ui-dist
copy ./node_modules/swagger-ui-dist  ./
```

Passos:
1.0 - Editor swagger.io
1.1 - Clone swagger editor
2 - Write documantation in Yaml
3.0 - Download json file
3.1 - Write gulp task to generate json file
4.0 - Download swagger UI dist
5.0 - Import json file
6 - Upload to your website


Adding Swagger To Existing Node.js Project (Bottom-up approach)
```
npm i swagger-ui-express -S

var swaggerUi = require('swagger-ui-express'),
    swaggerDocument = require('./swagger.json');

app.use('/api-docs', swaggerUi.serve, swaggerUi.setup(swaggerDocument));
app.use('/api/v1', router);
```

Running the image from DockerHub
```
docker pull swaggerapi/swagger-editor
docker run -d -p 80:8080 swaggerapi/swagger-editor
```

http://localhost

Building and running an image locally
Install npm packages (if needed)
`npm install`
Build the app
`npm run build`
Build an image
`docker build -t swagger-editor .`
Run the container
`docker run -d -p 80:8080 swagger-editor`

http://localhost