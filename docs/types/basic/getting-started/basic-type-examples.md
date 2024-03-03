---
description: Это базовое руководство для разработчиков React, знакомящихся с TypeScript
---

# Типизация пропсов компонентов

Это базовое руководство для разработчиков React, знакомящихся с TypeScript.

## Примеры основных типов реквизитов

Список типов TypeScript, которые вы, скорее всего, будете использовать в приложении на React+TypeScript:

```ts
type AppProps = {
    message: string;
    count: number;
    disabled: boolean;

    /** массив типа! */
    names: string[];

    /** строковые литералы для указания точных строковых значений,
     *  а также тип объединения для их объединения */
    status: 'waiting' | 'success';

    /** объект с известными свойствами (но может иметь
     *  и другие во время выполнения) */
    obj: {
        id: string;
        title: string;
    };

    /** массив объектов */
    objArr: {
        id: string;
        title: string;
    }[];

    /** любое непервичное значение - не может получить доступ ни к
     *  одному свойству (но полезно как заполнитель) */
    obj2: object;

    /** интерфейс без обязательных свойств - (за исключением
     *  таких вещей, как `React.Component<{}, State>`) */
    obj3: {};

    /** объект dict с любым количеством свойств одного типа */
    dict1: {
        [key: string]: MyTypeHere;
    };
    dict2: Record<string, MyTypeHere>; // эквивалентно dict1

    /** функция, которая ничего не принимает и не возвращает */
    onClick: () => void;

    /** функция с именованным свойством */
    onChange: (id: number) => void;

    /** синтаксис типа функции, принимающей событие */
    onChange: (
        event: React.ChangeEvent<HTMLInputElement>
    ) => void;

    /** альтернативный синтаксис типа функции, принимающей событие */
    onClick(
        event: React.MouseEvent<HTMLButtonElement>
    ): void;

    /** любую функцию, если вы не вызываете ее (не рекомендуется) */
    onSomething: Function;

    /** необязательный параметр */
    optional?: OptionalType;

    /** при передаче функции установщика состояния, возвращаемой
     *  `useState`, дочернему компоненту. В качестве примера можно
     *  привести `number`, замените его на любой тип вашего состояния */
    setState: React.Dispatch<React.SetStateAction<number>>;
};
```

### `object` как непримитивный тип

`object` - частый источник недопонимания в TypeScript. Он означает не "любой объект", а скорее "любой непримитивный тип", что означает, что он представляет все, что не является `number`, `string`, `boolean`, `symbol`, `null` или `undefined`.

Типизация "любого непримитивного значения", скорее всего, не является тем, что вы должны делать в React, а значит, вы, вероятно, не будете часто использовать `object`.

### Пустой интерфейс, `{}` и `Object`

Пустой интерфейс, `{}` и `Object` представляют собой "любое ненулевое значение", а не "пустой объект", как вы могли бы подумать. [Использование этих типов является частым источником недоразумений и не рекомендуется](https://typescript-eslint.io/rules/no-empty-interface/).

```ts
// эквивалентно `type AnyNonNullishValue = {}`
// или `type AnyNonNullishValue = Object`
interface AnyNonNullishValue {}

let value: AnyNonNullishValue;

// Все это хорошо, но, возможно, не совсем ожидаемо.
value = 1;
value = 'foo';
value = () => alert('foo');
value = {};
value = { foo: 'bar' };

// это ошибки
value = undefined;
value = null;
```

## Примеры полезных типов пропсов React

Актуально для компонентов, которые принимают другие компоненты React в качестве пропсов.

```ts
export declare interface AppProps {
    // лучше всего, принимает все, что может вывести React
    children?: React.ReactNode;

    // Один элемент React
    childrenElement: React.JSX.Element;

    // для передачи через пропсы стиля
    style?: React.CSSProperties;

    // события формы! общим параметром является тип event.target
    onChange?: React.FormEventHandler<HTMLInputElement>;

    // чтобы обезличить все свойства элемента button
    // и явно не пересылать его ref
    props: Props & React.ComponentPropsWithoutRef<'button'>;

    // для обезличивания всех пропсов MyButtonForwardedRef
    // и явной переадресации его ссылки
    props2: Props &
        React.ComponentPropsWithRef<MyButtonWithForwardRef>;
}
```

!!!info "Small `React.ReactNode` edge case before React 18"

    До обновления [React 18 type updates](https://github.com/DefinitelyTyped/DefinitelyTyped/pull/56210) этот код проверялся, но при этом возникала ошибка времени выполнения:

    ```ts
    type Props = {
    	children?: React.ReactNode;
    };

    function Comp({ children }: Props) {
    	return <div>{children}</div>;
    }
    function App() {
    	// Before React 18: Runtime error "Objects are not valid as a React child"
    	// After React 18: Typecheck error "Type '{}' is not assignable to type 'ReactNode'"
    	return <Comp>{{}}</Comp>;
    }
    ```

    Это происходит потому, что `ReactNode` включает `ReactFragment`, который разрешал тип `{}` до React 18.

    [Спасибо @pomle за то, что поднял этот вопрос.](https://github.com/typescript-cheatsheets/react/issues/357)

!!!info "React.JSX.Element или React.ReactNode?"

    Цитата [@ferdaber](https://github.com/typescript-cheatsheets/react/issues/57): Более техническое объяснение заключается в том, что действительный узел React - это не то же самое, что возвращает `React.createElement`. Независимо от того, что в итоге выводит компонент, `React.createElement` всегда возвращает объект, который является интерфейсом `React.JSX.Element`, а `React.ReactNode` - это набор всех возможных возвращаемых значений компонента.

    -   `React.JSX.Element` -> Возвращаемое значение `React.createElement`
    -   `React.ReactNode` -> Возвращаемое значение компонента

[Еще обсуждение: Где ReactNode не пересекается с React.JSX.Element](https://github.com/typescript-cheatsheets/react/issues/129)

[Что-то добавить? Подать вопрос](https://github.com/typescript-cheatsheets/react/issues/new).

## Типы или интерфейсы?

Вы можете использовать либо типы, либо интерфейсы для создания типов реквизитов и состояний, поэтому естественно возникает вопрос - что вы используете?

### TL;DR

Используйте интерфейс, пока не понадобится тип - [orta](https://twitter.com/orta/status/1356129195835973632?s=20).

### Другие советы

Вот полезное эмпирическое правило:

-   всегда используйте `interface` для определения публичных API при создании библиотеки или сторонних определений типов, так как это позволяет потребителю расширить их через _объединение деклараций_, если некоторые определения отсутствуют.

-   рассмотрите возможность использования `type` для реквизитов и состояний React-компонентов, для согласованности и потому что это более ограничено.

Подробнее об обосновании этого правила можно прочитать в [Interface vs Type alias in TypeScript 2.7](https://medium.com/@martin_hotell/interface-vs-type-alias-in-typescript-2-7-2a8f1777af4c).

Руководство по TypeScript теперь также включает руководство по [Differences Between Type Aliases and Interfaces](https://www.typescriptlang.org/docs/handbook/2/everyday-types.html#differences-between-type-aliases-and-interfaces).

!!!note "Примечание"

    При масштабировании есть причины по производительности предпочесть интерфейсы ([см. официальные заметки Microsoft об этом](https://github.com/microsoft/TypeScript/wiki/Performance#preferring-interfaces-over-intersections)), но [воспринимайте это с долей соли](https://news.ycombinator.com/item?id=25201887)

Типы полезны для объединения типов (например, `type MyType = TypeA | TypeB`), в то время как интерфейсы лучше использовать для объявления словарных форм и последующего `имплементирования` или `расширения` их.

### Полезная таблица для сравнения типов и интерфейсов

Это тонкая тема, не стоит на ней слишком зацикливаться. Вот удобная таблица:

| Аспект                                               | Тип | Интерфейс |
| ---------------------------------------------------- | :-: | :-------: |
| Может описывать функции                              | ✅  |    ✅     |
| Можно описать конструкторы                           | ✅  |    ✅     |
| Может описывать кортежи                              | ✅  |    ✅     |
| Интерфейсы могут расширять его                       | ⚠️  |    ✅     |
| Классы могут расширять его                           | 🚫  |    ✅     |
| Классы могут реализовывать его (`implements`)        | ⚠️  |    ✅     |
| Может пересекаться с другим подобным                 | ✅  |    ⚠️     |
| Может создавать союз с другими себе подобными        | ✅  |    🚫     |
| Можно использовать для создания сопоставленных типов | ✅  |    🚫     |
| Может быть сопоставлен с сопоставленными типами      | ✅  |    ✅     |
| Расширяется в сообщениях об ошибках и журналах       | ✅  |    🚫     |
| Может быть дополнен                                  | 🚫  |    ✅     |
| Может быть рекурсивным                               | ⚠️  |    ✅     |

⚠️ В некоторых случаях

<small>:material-information-outline: Источник &mdash; [https://react-typescript-cheatsheet.netlify.app/docs/basic/getting-started/basic_type_example](https://react-typescript-cheatsheet.netlify.app/docs/basic/getting-started/basic_type_example)</small>
