# Documentation-GA4-Firestore-NextJs

Documentação de integração do **Google Analytics 4 (GA4)** em aplicações **Next.js + TypeScript**, utilizando uma estrutura preparada para centralização de eventos e conformidade com a LGPD.

---

# Sumário

* Configuração do Google Analytics
* Consentimento LGPD
* Estrutura do projeto
* Configuração da variável de ambiente
* Componente principal do Google Analytics
* Inserindo no RootLayout
* Arquivo central de eventos
* ProductAnalytics
* Eventos recomendados para E-commerce
* Testes e validação
* Realtime do GA4
* Boas práticas
* Resultado final

---

# Configuração do Google Analytics

## Criando a propriedade

1. Acesse o Google Analytics.
2. Entre na propriedade desejada.
3. Vá em:

```text
Administrador

Fluxos de dados (Data Streams)
```

4. Selecione o fluxo referente ao site.
5. Copie o **Measurement ID**.

Exemplo:

```text
G-XXXXXXXXXX
```

---

## Ativando o Enhanced Measurement

No fluxo de dados, mantenha habilitada a opção:

```text
Enhanced Measurement
```

Ela permite rastrear automaticamente:

* Visualizações de páginas
* Scroll da página
* Downloads
* Cliques em links externos
* Pesquisas internas
* Interações com vídeos

---

# Consentimento LGPD

## Aviso importante

Como o Google Analytics coleta informações relacionadas à navegação, é recomendado solicitar o consentimento do usuário antes de iniciar a coleta.

Dependendo do caso e da interpretação jurídica aplicável, a ausência de consentimento pode gerar problemas relacionados à LGPD.

---

## Exemplo simplificado de modal

```tsx
"use client";

export default function ModalLGPD() {

    const handleAccept = () => {
        localStorage.setItem("lgpd_consent", "accepted");
    };

    const handleReject = () => {
        localStorage.setItem("lgpd_consent", "rejected");
    };

    return (
        <>
            <button onClick={handleAccept}>
                Aceitar
            </button>

            <button onClick={handleReject}>
                Recusar
            </button>
        </>
    );
}
```

---

# Estrutura do projeto

Uma organização recomendada:

```text
src
│
├── components
│     └── GoogleAnalytics
│            ├── GoogleAnalytics.tsx
│            ├── ProductAnalytics.tsx
│
├── lib
│     └── gtag.ts
│
└── app
```

Essa estrutura facilita manutenção e escalabilidade.

---

# Configuração da variável de ambiente

Arquivo:

```text
.env.local
```

Exemplo:

```env
NEXT_PUBLIC_GA_ID=G-XXXXXXXXXX
```

---

## Nunca exponha

* Chaves privadas
* Tokens
* Credenciais
* URLs internas sensíveis
* Variáveis secretas

---

# Componente principal do Google Analytics

Arquivo:

```text
components/GoogleAnalytics/GoogleAnalytics.tsx
```

Responsável por:

* Carregar o script do GA4.
* Verificar se o usuário aceitou os cookies.
* Iniciar a coleta somente após o consentimento.

Exemplo simplificado:

```tsx
"use client";

import Script from "next/script";

export default function GoogleAnalytics() {

    return (
        <>
            <Script
                src={`https://www.googletagmanager.com/gtag/js?id=${process.env.NEXT_PUBLIC_GA_ID}`}
                strategy="afterInteractive"
            />

            <Script id="google-analytics">

                {`
                    window.dataLayer = window.dataLayer || [];

                    function gtag(){
                        dataLayer.push(arguments);
                    }

                    gtag('js', new Date());

                    gtag('config', '${process.env.NEXT_PUBLIC_GA_ID}');
                `}
            </Script>

        </>
    );
}
```

---

# Inserindo no RootLayout

Exemplo:

```tsx
<body>

    <GoogleAnalytics />

    {children}

</body>
```

Dessa forma o Google Analytics ficará disponível em toda a aplicação.

---

# Arquivo central de eventos

Arquivo:

```text
src/lib/gtag.ts
```

Este arquivo é responsável por centralizar todos os eventos do sistema.

Exemplo:

```tsx
export const GA_MEASUREMENT_ID =
    process.env.NEXT_PUBLIC_GA_ID!;

declare global {

    interface Window {
        gtag: (...args: any[]) => void;
    }

}

export const pageview = (url: string) => {

    window.gtag(
        "config",
        GA_MEASUREMENT_ID,
        {
            page_path: url
        }
    );

};

export const event = (
    action: string,
    params?: Record<string, any>
) => {

    window.gtag(
        "event",
        action,
        params
    );

};
```

---

## Vantagens da centralização

Ao invés de utilizar:

```tsx
window.gtag(...)
```

espalhado por diversos componentes, todos os eventos podem ser enviados através de:

```tsx
event("nome_do_evento")
```

Facilitando:

* Manutenção;
* Escalabilidade;
* Reutilização;
* Organização do código.

---

# ProductAnalytics

Arquivo:

```text
components/GoogleAnalytics/ProductAnalytics.tsx
```

Esse componente tem como objetivo centralizar todos os eventos referentes aos produtos.

Exemplo:

```tsx
"use client";

import { useEffect } from "react";
import { event } from "@/lib/gtag";

export default function ProductAnalytics({ product }: any) {

    useEffect(() => {

        event("view_item", {

            currency: "BRL",

            value: product.price,

            items: [
                {
                    item_id: product.id,
                    item_name: product.name,
                    price: product.price
                }
            ]

        });

    }, []);

    return null;

}
```

Depois basta chamar:

```tsx
<ProductAnalytics product={product} />
```

Assim, sempre que um usuário entrar na página do produto, o evento será enviado automaticamente.

---

# Eventos recomendados para E-commerce

## select_item

Disparado quando o usuário clica em um produto.

Exemplo:

```tsx
event("select_item", {

    items: [
        {
            item_id: product.id,
            item_name: product.name,
            price: product.price
        }
    ]

});
```

Permite descobrir:

* Produtos mais clicados;
* Produtos com maior interesse;
* Produtos mais visualizados.

---

## view_item

Disparado quando o usuário acessa a página do produto.

Exemplo:

```tsx
event("view_item", {

    currency: "BRL",

    value: product.price,

    items: [
        {
            item_id: product.id,
            item_name: product.name,
            price: product.price
        }
    ]

});
```

Permite analisar:

* Produtos mais visualizados;
* Interesse dos usuários;
* Conversão entre clique e compra.

---

## add_to_cart

Disparado quando um produto é adicionado ao carrinho.

```tsx
event("add_to_cart", {

    currency: "BRL",

    value: product.price,

    items: [
        {
            item_id: product.id,
            item_name: product.name,
            quantity: 1,
            price: product.price
        }
    ]

});
```

Permite descobrir:

* Produtos mais adicionados ao carrinho;
* Produtos com maior intenção de compra.

---

## remove_from_cart

Disparado quando um item é removido do carrinho.

```tsx
event("remove_from_cart", {

    currency: "BRL",

    value: product.price,

    items: [
        {
            item_id: product.id,
            item_name: product.name,
            quantity: 1,
            price: product.price
        }
    ]

});
```

Permite identificar:

* Produtos com maior desistência;
* Possíveis problemas de preço ou interesse.

---

## add_to_favorites

Evento personalizado.

```tsx
event("add_to_favorites", {

    items: [
        {
            item_id: product.id,
            item_name: product.name
        }
    ]

});
```

Permite descobrir:

* Produtos mais salvos;
* Produtos mais desejados.

---

## view_cart

Disparado quando o usuário abre o carrinho.

```tsx
event("view_cart", {

    currency: "BRL",

    value: total,

    items: [...]
});
```

Permite analisar:

* Quantidade de acessos ao carrinho;
* Produtos presentes no carrinho.

---

## begin_checkout

Disparado quando o usuário inicia o processo de finalização da compra.

```tsx
event("begin_checkout", {

    currency: "BRL",

    value: total,

    items: [...]
});
```

Permite medir:

* Quantidade de usuários que chegaram ao checkout;
* Taxa de abandono.

---

## purchase

Evento recomendado para confirmar uma compra finalizada.

```tsx
event("purchase", {

    currency: "BRL",

    value: total,

    transaction_id: "123456",

    items: [...]

});
```

Permite analisar:

* Receita;
* Produtos vendidos;
* Conversão do e-commerce.

---

# Testando os eventos

Abra o Console do navegador:

```javascript
window.dataLayer
```

Ou:

```javascript
window.dataLayer.filter(x => x[0] === "event")
```

Exemplo:

```javascript
[
 ['event','select_item', {...}],
 ['event','view_item', {...}],
 ['event','add_to_cart', {...}],
 ['event','view_cart', {...}]
]
```

---

# Realtime do Google Analytics

No painel do Google Analytics:

```text
Relatórios

Realtime
```

É possível visualizar:

* Usuários ativos;
* Eventos em tempo real;
* Páginas acessadas;
* Produtos visualizados;
* Interações ocorrendo naquele momento.

---

# Boas práticas

## Centralize os eventos

Prefira:

```tsx
event("add_to_cart")
```

Ao invés de:

```tsx
window.gtag(...)
```

espalhado pelo projeto.

---

## Utilize componentes específicos

Exemplos:

```text
ProductAnalytics.tsx

PageAnalytics.tsx

CheckoutAnalytics.tsx
```

Isso facilita:

* Organização;
* Reutilização;
* Escalabilidade.

---

## Utilize os nomes oficiais do GA4

Eventos recomendados:

```text
view_item

select_item

add_to_cart

remove_from_cart

view_cart

begin_checkout

purchase
```

Esses eventos já são reconhecidos automaticamente pelo Google Analytics.

---

## Evite duplicidade de eventos

Utilize:

```tsx
useRef(false)
```

ou:

```tsx
useEffect(() => {

}, []);
```

para evitar múltiplos disparos durante renderizações.

---

# Resultado

Com essa estrutura o projeto possui:

Integração com Google Analytics 4.

Compatibilidade com Next.js e TypeScript.

Consentimento LGPD.

Eventos centralizados.

Código organizado.

Estrutura escalável.

Rastreamento completo para E-commerce.

Facilidade para futuras expansões.

Melhor manutenção do sistema.

Arquitetura mais limpa e profissional.
