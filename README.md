# Rech Swagger Express

Esta biblioteca permite que você forneça documentação de API gerada dinâmicamente pelo  [swagger-ui](https://swagger.io/tools/swagger-ui/) através do Express, baseados em um documento `api.json`. O resultado é uma documentação dinâmica acessada no servidor através de uma rota.

A versão do Swagger é definida automaticamente através da biblioteca [rech-swagger-dist](https://github.com/RechInformatica/rech-swagger-dist), que é uma dependência desta biblioteca.

- [Como instalar](#como-instalar)
- [Como usar](#como-usar)
    - [swagger-jsdoc](#swagger-jsdoc)
    - [Swagger Explorer](#swagger-explorer)
    - [Customizando o Swagger](#customizando-o-swagger)
    - [CSS customizado](#css-customizado)
    - [CSS customizado com um arquivo](#css-customizado-com-um-arquivo)
    - [Javascript personalizado](#javascript-personalizado)
    - [Carregar documento usando url](#carregar-documento-usando-url)
    - [Carregar documento com um arquivo yaml](#carregar-documento-com-um-arquivo-yaml)
    - [Modificar documento on the fly antes de carregar](#modificar-documento-on-the-fly-antes-de-carregar)
- [Requisitos](#requisitos)
- [Testes](#testes)

## Como instalar

**Antes de instalar a biblioteca é necessário ter configurado o proxy interno do NPM. Caso ainda não esteja configurado verifique a [wiki](http://intranet/wiki/index.php/Proxy_NPM).**

Instale através do NPM:

```bash
npm install rech-swagger-express
```

## Como usar

Configurando a biblioteca no Express `app.js`
```javascript
const express = require('express');
const app = express();
const rechSwaggerExpress = require('rech-swagger-express');
const apiDocument = require('./api.json');

app.use('/api-docs', rechSwaggerExpress.serve, rechSwaggerExpress.setup(apiDocument));
```

ou se você está usando o Express router

```javascript
const router = require('express').Router();
const rechSwaggerExpress = require('rech-swagger-express');
const apiDocument  = require('./api.json');

router.use('/api-docs', rechSwaggerExpress.serve);
router.get('/api-docs', rechSwaggerExpress.setup(apiDocument));
```

Acesse http://`<app_host>`:`<app_port>`/api-docs no seu navegador para visualizar a documentação.

Se você prefere configurar as rotas baseado no documento Swagger, verifique a biblioteca [swagger-express-router](https://www.npmjs.com/package/swagger-express-router).

### swagger-jsdoc

Se você está usando a biblioteca [swagger-jsdoc](https://www.npmjs.com/package/swagger-jsdoc), passe o valor de swaggerSpec na função de configuração.

```javascript
// Inicialização do swagger-jsdoc -> retorna uma especificação swagger validada em formato JSON
const swaggerSpec = swaggerJSDoc(options);

app.use('/api-docs', rechSwaggerExpress.serve, rechSwaggerExpress.setup(swaggerSpec));
```

### Swagger Explorer

Por padrão, o campo de pesquisa na barra superior fica oculto, para exibi-lo passe `true` na propriedade `explorer` nas opções de configuração.

```javascript
const express = require('express');
const app = express();
const rechSwaggerExpress = require('rech-swagger-express');
const apiDocument = require('./api.json');

var options = {
  explorer: true
};

app.use('/api-docs', rechSwaggerExpress.serve, rechSwaggerExpress.setup(apiDocument, options));
```

### Customizando o Swagger

Para passar opções customizadas para o client Swagger (por exemplo, validatorUrl), passe um objeto contendo a propriedade `swaggerOptions` nas opções de configuração:

```javascript
const express = require('express');
const app = express();
const rechSwaggerExpress = require('rech-swagger-express');
const apiDocument = require('./api.json');

var options = {
  swaggerOptions: {
    validatorUrl: null
  }
};

app.use('/api-docs', rechSwaggerExpress.serve, rechSwaggerExpress.setup(apiDocument, options));
```

Para verificar as opções de configuração disponíveis, consulte as [configurações do Swagger](https://github.com/swagger-api/swagger-ui/blob/master/docs/usage/configuration.md)

### CSS customizado

Para customizar o CSS da página, você pode utilizar a propriedade `customCss` nas opções de configuração:

Exemplo de como ocultar o header do Swagger:

```javascript
const express = require('express');
const app = express();
const rechSwaggerExpress = require('rech-swagger-express');
const apiDocument = require('./api.json');

var options = {
  customCss: '.swagger-ui .topbar { display: none }'
};

app.use('/api-docs', rechSwaggerExpress.serve, rechSwaggerExpress.setup(apiDocument, options));
```

### CSS customizado com um arquivo

Você também pode passar a url de um arquivo css, o valor precisa ser a url pública do arquivo e pode ser relativa ou absoluta em relação ao caminho do Swagger.

```javascript
const express = require('express');
const app = express();
const rechSwaggerExpress = require('rech-swagger-express');
const apiDocument = require('./api.json');

var options = {
  customCssUrl: '/custom.css'
};

app.use('/api-docs', rechSwaggerExpress.serve, rechSwaggerExpress.setup(apiDocument, options));
```

### Javascript personalizado

Se você deseja ter controle total sobre o HTML, é possível utilizar seu próprio arquivo javascript, é possível utilizar o caminho absoluto ou relativo do arquivo. O valor precisa ser a url pública do arquivo javascript.

```javascript
const express = require('express');
const app = express();
const rechSwaggerExpress = require('rech-swagger-express');
const apiDocument = require('./api.json');

var options = {
  customJs: '/custom.js'
};

app.use('/api-docs', rechSwaggerExpress.serve, rechSwaggerExpress.setup(apiDocument, options));
```

### Carregar documento usando url

É possível carregar seu documento de especificação através de url, ao invés de injetar o mesmo. Passe `null` como primeiro parâmetro e passe a url absoluta ou relativa na propriedade `swaggerOptions` nas opções de configuração.

```javascript
const express = require('express');
const app = express();
const rechSwaggerExpress = require('rech-swagger-express');

var options = {
  swaggerOptions: {
    url: 'http://petstore.swagger.io/v2/swagger.json'
  }
}

app.use('/api-docs', rechSwaggerExpress.serve, rechSwaggerExpress.setup(null, options));
```

Para carregar vários arquivos de especificação através de urls como dropdown na barra superior, passe um array de objetos com as proprieades `name` e `url` na propriedade `urls` nas opções de configuração.

```javascript
const express = require('express');
const app = express();
const rechSwaggerExpress = require('rech-swagger-express');

var options = {
  explorer: true,
  swaggerOptions: {
    urls: [
      {
        url: 'http://petstore.swagger.io/v2/swagger.json',
        name: 'Spec1'
      },
      {
        url: 'http://petstore.swagger.io/v2/swagger.json',
        name: 'Spec2'
      }
    ]
  }
}

app.use('/api-docs', rechSwaggerExpress.serve, rechSwaggerExpress.setup(null, options));
```

Lembre de configurar a opção `explorer` como `true` nas opções de configuração para deixar o dropdown visível.


### Carregar documento com um arquivo yaml

Para carregar a especificação através de um arquivo yml é necessário uma biblioteca capaz de converter yaml para json, por exemplo `yamljs`.

    npm install --save yamljs

```javascript
const express = require('express');
const app = express();
const rechSwaggerExpress = require('rech-swagger-express');
const YAML = require('yamljs');
const apiDocument = YAML.load('./api.yaml');

app.use('/api-docs', rechSwaggerExpress.serve, rechSwaggerExpress.setup(apiDocument));
```

### Modificar documento on the fly antes de carregar

Para definir o host dinamicamente, ou qualquer outro conteúdo, é possível alterar dinâmicamente o documento de especificação e passar o mesmo para continuar a request; para fazer isso não passe o documento de especificação nas opções de configuração e a biblioteca irá procurar pelo documento de especificação no objeto `request`.

```javascript
const express = require('express');
const app = express();
const rechSwaggerExpress = require('rech-swagger-express');
const apiDocument = require('./api.json');

app.use('/api-docs', function(req, res, next){
    apiDocument.host = req.get('host');
    req.swaggerDoc = apiDocument;
    next();
}, rechSwaggerExpress.serve, rechSwaggerExpress.setup());
```

## Requisitos

* Node v10.10.32 ou superior
* Express 4 ou superior

## Testes

* Instale o phantom
* `npm install`
* `npm test`
