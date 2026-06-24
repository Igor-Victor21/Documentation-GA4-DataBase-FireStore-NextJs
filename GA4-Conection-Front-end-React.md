# Conexão com o Google Analytics no Front-end utilizando React e Vite

### 1 - Instalando a biblioteca

```txt
npm install recharts axios
```

##

### 2 - Organização 

Criei uma nova pasta dentro da pasta src chamada `service`, onde irei chamar todas as rotas feita no back-end do GA4, para o meu front-end realizar todas as requisições da API tudo em apenas um unico lugar, assim otimizando o tempo para chamadas de funções entre outros components

Código: 

```js
Código:
```

Além disso fiz dentro de components criei uma nova pasta apenas para os components do google analytics, chamada de `Analytics` com isso caso tenha que realizar alguma manutenção em algum dos components do GA4, será muito mais facíl de encontrar o component em si.

Ficou assim a organização:

```txt
src
│
├── pages
│     └── UserAdmin.jsx (Onde irá ficar o dashboard do GA4)
│
├── services
│     └── analyticsService.js
│
├── components
│     └── Analytics
│             ├── DashboardCards.jsx
│             ├── FunnelChart.jsx
│             ├── LineUsersChart.jsx
│             ├── ProductsViewed.jsx
│             ├── ProductsCart.jsx
│             ├── CitiesTable.jsx
│             └── ConversionCard.jsx
```

##

### Código de todos os arquivos

OBS: Fazendo o Css do projeto atualmente... 



