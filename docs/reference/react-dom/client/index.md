---
description: API react-dom/client позволяют отображать компоненты React на клиенте (в браузере). Эти API обычно используются на верхнем уровне вашего приложения для инициализации дерева React
---

# Client React DOM API

<big>API `react-dom/client` позволяют отображать компоненты React на клиенте (в браузере). Эти API обычно используются на верхнем уровне вашего приложения для инициализации дерева React. Фреймворк [framework](../../../learn/start-a-new-react-project.md#production-grade-react-frameworks) может вызвать их для вас. Большинству ваших компонентов не нужно их импортировать или использовать.</big>

## Клиентские API

-   [`createRoot`](./createRoot.md) позволяет создать корень для отображения компонентов React внутри узла DOM браузера.
-   [`hydrateRoot`](./hydrateRoot.md) позволяет отображать компоненты React внутри DOM-узла браузера, HTML-содержимое которого было предварительно сгенерировано [`react-dom/server`](../server/index.md).

## Поддержка браузеров

React поддерживает все популярные браузеры, включая Internet Explorer 9 и выше. Для старых браузеров, таких как IE 9 и IE 10, требуются некоторые полифиллы.

<small>:material-information-outline: Источник &mdash; <https://react.dev/reference/react-dom/client></small>
