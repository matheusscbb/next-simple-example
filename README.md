![Next.js](https://i.imgur.com/2LZTaCT.png)

Uma aplicação simples para validar o Next.js, validando alguns tópicos.   

Exemplo criado com base no guia oficial do Next.js, que pode ser encontrado em seu site: [learn Next.js](https://nextjs.org/learn/basics/getting-started)

## **Start Aplication**
```
$ npm install
$ npm run dev
```

### Rotas
Enquanto o projeto estiver rodando localmente, através do `npm run dev`, o roteamento da aplicação é controlado pelo next, baseado simplesmente nos diretórios e arquivos dentro do pages.   

## **Deploy**

### **Next.js Configuration**
O `package.json` deve possuir os scripts de start e build do next:

*package.json*
```
"scripts": { 
  "build": "next build",  
  "start": "next start",  
}
```

Para cuidar das rotas deve ser feito um mapeamento. Criando o arquivo `next.config.js` com um conteúdo similar a esse:
```
module.exports = {
  exportPathMap: async function() {
    const paths = {
      '/': { page: '/' }
    };

    return paths;
  }
};
```

### **Nginx’s Configuration**
Com o packge preparado o projeto já está pronto para o deploy. Se não tiver o Nginx pode ser facilmente instalado com:
```
$ sudo apt update
$ sudo apt install nginx
```

Pode checar se o Nginx está rodando, em seu linux, com:

```
$ systemctl status nginx
```

O seu start pode ser feito:
```
$ service nginx start
$ systemctl start nginx
$ sudo nginx -s start
```

Com a instalação completa vamos editar o arquivo de configuração:

```
# MACOS
$ vim /usr/local/etc/nginx/nginx.conf

# UBUNTU
$ /etc/nginx/sites-available/default
```

```
# No fim do aqruivo, adicione:
server {
  server_name     domain.com www.domain.com;

  location / {
    # default port, se usar um custom next server com porta diferente.
    proxy_pass http://localhost:3000;

    proxy_http_version 1.1;
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection 'upgrade';
    proxy_set_header Host $host;
    proxy_cache_bypass $http_upgrade;

    # if you have try_files like this, remove it from our block
    # otherwise next app will not work properly
    # try_files $uri $uri/ =404;
  }
}
```

Depois de alterar restart o Nginx:
```
$ service nginx restart
$ systemctl restart nginx
$ sudo nginx -s reload
```

**Subindo a aplicação com PM2:**
Para realizar a preparação e já subir user:
```
npm run build && pm2 start npm --name "next" -- start
```

Para verificar use `pm2 list` e caso estaja com erro use o `pm2 log`:

##### *Erro com node resolvido com `npm config set scripts-prepend-node-path true`*.

Deploy simples baseado no artigo do medium: [Deploying a NextJS App in Production with Custom Server using Nginx and PM2](https://medium.com/@indiesk/deploying-a-nextjs-app-in-production-with-custom-server-using-nginx-and-pm2-786ccf9444c5), para mais informaçoes acesse ele.



