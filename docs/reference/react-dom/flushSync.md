---
description: flushSync позволяет вам заставить React синхронно промывать любые обновления внутри предоставленного обратного вызова. Это гарантирует, что DOM будет обновлен немедленно
---

# flushSync

!!!warning ""

    Использование `flushSync` является необычным и может ухудшить производительность вашего приложения.

<big>`flushSync` позволяет вам заставить React синхронно промывать любые обновления внутри предоставленного обратного вызова. Это гарантирует, что DOM будет обновлен немедленно.</big>

```js
flushSync(callback);
```

## Описание {#reference}

### `flushSync(callback)` {#flushsync}

Вызовите `flushSync`, чтобы заставить React промыть все ожидающие работы и синхронно обновить DOM.

```js
import { flushSync } from 'react-dom';

flushSync(() => {
    setSomething(123);
});
```

В большинстве случаев можно обойтись без `flushSync`. Используйте `flushSync` в крайнем случае.

**Параметры**

-   `callback`: Функция. React немедленно вызовет этот обратный вызов и синхронно прошьет все содержащиеся в нем обновления. Он также может прошить все ожидающие обновления, или эффекты, или обновления внутри эффектов. Если обновление приостанавливается в результате этого вызова `flushSync`, фалбэки могут быть показаны заново.

**Возврат**

`flushSync` возвращает `undefined`.

**Предостережения**

-   `flushSync` может значительно снизить производительность. Используйте его осторожно.
-   `flushSync` может заставить ожидающие границы приостановки показать свое состояние `отката`.
-   `flushSync` может запускать отложенные эффекты и синхронно применять все содержащиеся в них обновления перед возвратом.
-   `flushSync` может промывать обновления вне обратного вызова, когда это необходимо для промывки обновлений внутри обратного вызова. Например, если есть ожидающие обновления от клика, React может промыть их, прежде чем промыть обновления внутри обратного вызова.

## Использование {#usage}

### Выгрузка обновлений для сторонних интеграций {#flushing-updates-for-third-party-integrations}

При интеграции со сторонним кодом, таким как API браузера или библиотеки пользовательского интерфейса, может потребоваться заставить React промывать обновления. Используйте `flushSync`, чтобы заставить React синхронно промывать любые обновления состояния внутри обратного вызова:

```js
flushSync(() => {
    setSomething(123);
});
// By this line, the DOM is updated.
```

Это гарантирует, что к моменту выполнения следующей строки кода React уже обновил DOM.

**Если ваше приложение использует только API React и не интегрируется со сторонними библиотеками, `flushSync не нужен.**

Однако он может быть полезен при интеграции со сторонним кодом, например, с API браузеров.

Некоторые браузерные API ожидают, что результаты внутри обратных вызовов будут записаны в DOM синхронно, к концу обратного вызова, чтобы браузер мог что-то сделать с отрисованной DOM. В большинстве случаев React делает это за вас автоматически. Но в некоторых случаях может потребоваться принудительное синхронное обновление.

Например, API браузера `onbeforeprint` позволяет изменять страницу непосредственно перед открытием диалога печати. Это полезно для применения пользовательских стилей печати, которые позволяют документу лучше отображаться при печати. В примере ниже вы используете `flushSync` внутри обратного вызова `onbeforeprint`, чтобы немедленно "прошить" состояние React в DOM. Тогда к моменту открытия диалога печати `isPrinting` отображает "да":

=== "App.js"

    ```js
    import { useState, useEffect } from 'react';
    import { flushSync } from 'react-dom';

    export default function PrintApp() {
    	const [isPrinting, setIsPrinting] = useState(false);

    	useEffect(() => {
    		function handleBeforePrint() {
    			flushSync(() => {
    				setIsPrinting(true);
    			});
    		}

    		function handleAfterPrint() {
    			setIsPrinting(false);
    		}

    		window.addEventListener(
    			'beforeprint',
    			handleBeforePrint
    		);
    		window.addEventListener(
    			'afterprint',
    			handleAfterPrint
    		);
    		return () => {
    			window.removeEventListener(
    				'beforeprint',
    				handleBeforePrint
    			);
    			window.removeEventListener(
    				'afterprint',
    				handleAfterPrint
    			);
    		};
    	}, []);

    	return (
    		<>
    			<h1>isPrinting: {isPrinting ? 'yes' : 'no'}</h1>
    			<button onClick={() => window.print()}>
    				Print
    			</button>
    		</>
    	);
    }
    ```

=== "CodeSandbox"

    <iframe src="https://codesandbox.io/embed/djmgw4?view=Editor+%2B+Preview&module=%2Fsrc%2FApp.js" style="width:100%; height: 500px; border:0; border-radius: 4px; overflow:hidden;" title="react.dev" allow="accelerometer; ambient-light-sensor; camera; encrypted-media; geolocation; gyroscope; hid; microphone; midi; payment; usb; vr; xr-spatial-tracking" sandbox="allow-forms allow-modals allow-popups allow-presentation allow-same-origin allow-scripts"></iframe>

Без `flushSync`, когда диалог печати будет отображать `isPrinting` как "нет". Это происходит потому, что React собирает обновления асинхронно и диалог печати отображается до обновления состояния.

!!!warning "`flushSync` может значительно снизить производительность"

    `flushSync` может значительно снизить производительность, и может неожиданно заставить ожидающие границы приостановки показать их состояние отката.

    В большинстве случаев `flushSync` можно избежать, поэтому используйте `flushSync` в крайнем случае.

<small>:material-information-outline: Источник &mdash; <https://react.dev/reference/react-dom/flushSync></small>
