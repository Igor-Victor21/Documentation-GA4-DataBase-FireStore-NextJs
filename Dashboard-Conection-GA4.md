# Conexão entre o Google Analytics

Dashboard feito em React (JSX), Node.js & Express  (TypeScript)

##

## Criado os arquivos

1 - Criando uma pasta nova dentro do src chamada `analytics`

2 - Criando os Arquivos

  - analytics.controller.ts
  - analytics.routes.ts
  - analytics.service.ts
  - credentials.json

3 - Instalando a biblioteca 

```text
npm install @google-analytics/data
```

4 - Crie uma variável de ambiente na .env

 - Nome: GA4_PROPERTY_ID=
 - Copie o seu PROPERTY ID
   - Admin
   - Property
   - Property details
   - Copie os números de `PROPERTY ID:`

5 - Crie uma conta no Google Cloud

```txt
https://console.cloud.google.com/
```

6 - Selecione o seu projeto no topo superior esquerdo onde está escrito `My First Project`

7 - Depois Clique no menu `☰`
 - IAM & Admin
 - Service Accounts

8 - Criando um novo serviço 
 - Clique em `+ Create service account`
 -  Nome: `ga4-dashboard`
 -  Clique: `Create and Continue`
 -  Selecione na opção `Basic` selecione `Viewer`
 -  Clique: `Continue`
 -  Depois Clique `Done` direto

9 - Gerar a chave JSON
 - Clique na lista que foi gerada `Bem na parte Azul que parece um Link`
 - Keys
 - Add Key
 - Create new key
 - JSON
 - Create
 - Será baixado um arquivo com o nome do seu projeto
 - Salva esse arquivo dentro do credentials.json (PS: Clique 2 vezes em cima do credentials.json e confirme o alerta)
 - Se tudo estiver certo quando você abrir o vs code vai estar todas as informações la dentro

10 - Salva o `credentials.json` dentro do `.gitignore`

```txt
src/analytics/credentials.json
```

11 - Dar acesso do Google Cloud para o Google Analytics

  - Abra o google analytics
  - Adimin
  - Property
  - Property access management
  - Clique em + no canto superior direito
  - Escolha Add users.
  - Cole o e-mail da Service Account que você criou no Google Cloud. Algo parecido com:
    ```txt
      ga4-dashboard@nome-do-projeto...
    ```
  - Em Direct roles and data restrictions, selecione: `Viewer`
  - Clique em Add.

12 - Ative o Google Analytics Data

Antes de ativar verifica apenas se está no projeto certo ai, caso esteja apenas clique em Enable

Link:

```txt
https://console.developers.google.com/apis/api/analyticsdata.googleapis.com/overview?project=864485220008
```

Depois de ativar espere uns 10 minutos

13 - Código dos arquivos da pasta:

- analytics.controller.ts

```ts
CÓDIGO:
```
  
- analytics.routes.ts
  
```ts
CÓDIGO:
```

- analytics.service.ts
  
```ts
CÓDIGO:
```

## DENTRO DA ROUTES DA ROOT

```ts
import analyticsRoutes from "./analytics/analytics.routes";
routes.use("/analytics", analyticsRoutes);
```

OBS: TESTE AS ROTAS NO POSTMAN ANTES DE COLOCAR QUALQUER SISTEMA DE SEGURANÇA

Ex rota: 

```txt
http://localhost:3000/analytics/dashboard
```

body: raw -> JSON

##

## Tudo testado agora é o frontend (REACT)
