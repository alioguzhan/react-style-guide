# Airbnb React/JSX Stil Rehberi

**(Turkish Translation of [Airbnb React/JSX Style Guide](https://github.com/airbnb/javascript/tree/master/react)**

*React and JSX konularına oldukça makul bir yaklaşım*

## içindekiler

  1. [Temel Kurallar](#temel-kurallar)
  1. [Class vs `React.createClass` vs stateless](#class-vs-reactcreateclass-vs-stateless)
  1. [Mixinler](#mixins)
  1. [Isimlendirme](#isimlendirme)
  1. [Deklarasyon](#deklarasyon)
  1. [Hizalama](#hizalama)
  1. [Tırnaklar](#tırnaklar)
  1. [Boşluk Kullanımı](#boşluk-kullanımı)
  1. [Proplar](#proplar)
  1. [Refler](#refler)
  1. [Parantezler](#parantezler)
  1. [Taglar](#tagler)
  1. [Metodlar](#metodlar)
  1. [Sıralama](#sıralama)
  1. [`isMounted`](#ismounted)

## Temel Kurallar

  - Her bir dosyada sadece bir adet React Componenti barındırın.
    - Ama, birden fazla [Stateless ya da Pure Components](https://facebook.github.io/react/docs/reusable-components.html#stateless-functions) aynı dosyada kullanılabilir. eslint: [`react/no-multi-comp`](https://github.com/yannickcr/eslint-plugin-react/blob/master/docs/rules/no-multi-comp.md#ignorestateless).
  - Daima JSX i kullanın.
  - Uygulamayı JSX olmayan bir dosyadan başlatmadığınız sürece `React.createElement` i kullanmayın.

## Class vs `React.createClass` vs stateless
  - Eğer dahili olarak state ve / veya ref ler varsa, `React.createClass` yerine `class extends React.Component` kullanmayı tercih edin. eslint: [`react/prefer-es6-class`](https://github.com/yannickcr/eslint-plugin-react/blob/master/docs/rules/prefer-es6-class.md) [`react/prefer-stateless-function`](https://github.com/yannickcr/eslint-plugin-react/blob/master/docs/rules/prefer-stateless-function.md)

    ```jsx
    // kötü
    const Listing = React.createClass({
      // ...
      render() {
        return <div>{this.state.hello}</div>;
      }
    });

    // iyi
    class Listing extends React.Component {
      // ...
      render() {
        return <div>{this.state.hello}</div>;
      }
    }
    ```

    Ve eğer state ya da ref leriniz yoksa, class lar içinde normal fonksiyonları (arrow fonksiyonları değil) tercih edin:

    ```jsx
    // kötü
    class Listing extends React.Component {
      render() {
        return <div>{this.props.hello}</div>;
      }
    }

    // kötü (Fonksiyon ismi çıkarımına güvenmek cesaret kırıcıdır.)
    const Listing = ({ hello }) => (
      <div>{hello}</div>
    );

    // iyi
    function Listing({ hello }) {
      return <div>{hello}</div>;
    }
    ```

## Mixins

  - [Mixinleri kullanmayın](https://facebook.github.io/react/blog/2016/07/13/mixins-considered-harmful.html).

  > Niye? Mixinler örtülü bağımlılıkları ortaya çıkarır, isim çatışmalarına neden olur ve snowballing karmaşıklığına neden olur. Mixinlerin kullanılmak istendiği bir çok durum, bileşenleri, daha üst düzey bileşenleri veya yardımcı modülleri kullanarak daha iyi yollarla başarılabilir.

## isimlendirme

  - **Dosya Uzantıları**: React Bileşenleri için `.jsx` uzantısını kullanın.
  - **Dosya İsimleri**: Dosya isimleri için PascalCase yöntemini kullanın. Mesela, `ReservationCard.jsx`.
  - **Reference İsimlendirmesi**: React Bileşenleri için PascalCase ve onların örnekleri için camelCase yöntemini kullanın. eslint: [`react/jsx-pascal-case`](https://github.com/yannickcr/eslint-plugin-react/blob/master/docs/rules/jsx-pascal-case.md)

    ```jsx
    // kötü
    import reservationCard from './ReservationCard';

    // iyi
    import ReservationCard from './ReservationCard';

    // kötü
    const ReservationItem = <ReservationCard />;

    // iyi
    const reservationItem = <ReservationCard />;
    ```

  - **Bileşen İsimlendirmesi**: Bileşen ismi olarak dosya adını kullanın. Örneğin, `ReservationCard.jsx` dosyası  `ReservationCard` adında bir bileşeni içermelidir. Ancak, bir klasörün kök bileşeni için; dosya ismi olarak `index.jsx` ve bileşen ismi olarak da klasörün ismini kullanın:

    ```jsx
    // kötü
    import Footer from './Footer/Footer';

    // iyi
    import Footer from './Footer/index';

    // iyi
    import Footer from './Footer';
    ```
  - **Üst-Sıralı Bileşen İsimlendirmesi**: Oluşturulan bileşen üzerinde `displayName` olarak Üst-Sıralı Bileşen ismi ve aktarılan bileşenin isminden oluşan bir karma isim kullanın. Örneğin, Üst-Sıralı bileşen üyesi olan `WithFoo ()` öğesi, `Bar` adındakı bir bileşen tarafından iletildiğinde, `displayName` i `withFoo(Bar)` olan bir bileşen üretmelidir.

    > Neden? Bir Bileşenin `displayName` i, geliştirici araçlarında veya hata mesajlarında kullanılabilir; ve bu ilişkiyi açıkça ifade eden bir değere sahip olması, insanların neler olduğunu anlamasına yardımcı olur.

    ```jsx
    // kötü
    export default function withFoo(WrappedComponent) {
      return function WithFoo(props) {
        return <WrappedComponent {...props} foo />;
      }
    }

    // iyi
    export default function withFoo(WrappedComponent) {
      function WithFoo(props) {
        return <WrappedComponent {...props} foo />;
      }

      const wrappedComponentName = WrappedComponent.displayName
        || WrappedComponent.name
        || 'Component';

      WithFoo.displayName = `withFoo(${wrappedComponentName})`;
      return WithFoo;
    }
    ```

  - **Propların İsimlendirmesi**:  DOM Bileşenlerine ait isimleri başka amaçlar için kullanmaktan kaçının.

    > Neden? İnsanlar `style` ve `className` gibi proplarin tek belirgin bir anlama gelmesini bekler. Bu API yi uygulamanızın bir alt kümesi için değiştirmek kodu daha az okunabilir ve daha az sürdürülebilir hale getirir, ve buglara sebep olabilir.

    ```jsx
    // kötü
    <MyComponent style="fancy" />

    // iyi
    <MyComponent variant="fancy" />
    ```

## Deklarasyon

  - Bileşenleri isimlendirmek için `displayName` kullanmayın. Onun yerine bileşeni referansa göre isimlendirin.

    ```jsx
    // kötü
    export default React.createClass({
      displayName: 'ReservationCard',
      // stuff goes here
    });

    // iyi
    export default class ReservationCard extends React.Component {
    }
    ```

## Hizalama

  - JSX sözdizimi için bu hizalama stillerini takip edin: [`react/jsx-closing-bracket-location`](https://github.com/yannickcr/eslint-plugin-react/blob/master/docs/rules/jsx-closing-bracket-location.md) [`react/jsx-closing-tag-location`](https://github.com/yannickcr/eslint-plugin-react/blob/master/docs/rules/jsx-closing-tag-location.md)

    ```jsx
    // kötü
    <Foo superLongParam="bar"
         anotherSuperLongParam="baz" />

    // iyi
    <Foo
      superLongParam="bar"
      anotherSuperLongParam="baz"
    />

    // Eğer proplar bir satıra sığıyorsa aynı satırda tutun.
    <Foo bar="bar" />

    // çocuklar normal olarak hizalanır.
    <Foo
      superLongParam="bar"
      anotherSuperLongParam="baz"
    >
      <Quux />
    </Foo>
    ```

## Tırnaklar

  - JSX nitelikleri için daima çift tırnak (`"`), ama diğer bütün JS için tek tırnak (`'`) kullanın. eslint: [`jsx-quotes`](http://eslint.org/docs/rules/jsx-quotes)

    > Neden? Normal HTML nitelikleri de tek tırnak yerine çift tırnak kullanır, JSX nitelikleri de bu düzeni yansıtır.

    ```jsx
    // kötü
    <Foo bar='bar' />

    // iyi
    <Foo bar="bar" />

    // kötü
    <Foo style={{ left: "20px" }} />

    // iyi
    <Foo style={{ left: '20px' }} />
    ```

## Boşluk Kullanımı

  - Kendi kendine kapanan taglar için daima tek boşluk kullanın. eslint: [`no-multi-spaces`](http://eslint.org/docs/rules/no-multi-spaces), [`react/jsx-tag-spacing`](https://github.com/yannickcr/eslint-plugin-react/blob/master/docs/rules/jsx-tag-spacing.md)

    ```jsx
    // kötü
    <Foo/>

    // çok kötü
    <Foo                 />

    // kötü
    <Foo
     />

    // good
    <Foo />
    ```

  - JSX süslü parantezlerini boşlukla şişirmeyin. eslint: [`react/jsx-curly-spacing`](https://github.com/yannickcr/eslint-plugin-react/blob/master/docs/rules/jsx-curly-spacing.md)

    ```jsx
    // kötü
    <Foo bar={ baz } />

    // iyi
    <Foo bar={baz} />
    ```

## Proplar

  - Prop isimleri için daima camelCase kullanın.

    ```jsx
    // kötü
    <Foo
      UserName="hello"
      phone_number={12345678}
    />

    // iyi
    <Foo
      userName="hello"
      phoneNumber={12345678}
    />
    ```

  - Açıkça `true` olarak belirtildiyse propun değerini atlayın. eslint: [`react/jsx-boolean-value`](https://github.com/yannickcr/eslint-plugin-react/blob/master/docs/rules/jsx-boolean-value.md)

    ```jsx
    // kötü
    <Foo
      hidden={true}
    />

    // iyi
    <Foo
      hidden
    />

    // iyi
    <Foo hidden />
    ```

  - `<img>` etiketlerine daima bir `alt` özelliği ekleyin. Eğer resim sunumsal ise, `alt` boş bir dize olabilir veya `<img>` etiketinde `<role="presentation"` bulunmalıdır.
   eslint: [`jsx-a11y/alt-text`](https://github.com/evcohen/eslint-plugin-jsx-a11y/blob/master/docs/rules/alt-text.md)

    ```jsx
    // kötü
    <img src="hello.jpg" />

    // iyi
    <img src="hello.jpg" alt="Me waving hello" />

    // iyi
    <img src="hello.jpg" alt="" />

    // iyi
    <img src="hello.jpg" role="presentation" />
    ```

  - `<img>` etiketinin `alt` özelliğinde "image", "photo", ya da  "picture" gibi kelimeler kullanmayın. eslint: [`jsx-a11y/img-redundant-alt`](https://github.com/evcohen/eslint-plugin-jsx-a11y/blob/master/docs/rules/img-redundant-alt.md)

    > Neden? Ekran okuyucuları zaten `img` elemanlarını resim olarak bildirdikleri için bu bilgiyi alt metne eklemenize gerek yoktur.

    ```jsx
    // kötü
    <img src="hello.jpg" alt="Picture of me waving hello" />

    // iyi
    <img src="hello.jpg" alt="Me waving hello" />
    ```

  - Yalnızca geçerli, soyut olmayan [ARIA roles](https://www.w3.org/TR/wai-aria-1.1/#role_definitions) kullanın. eslint: [`jsx-a11y/aria-role`](https://github.com/evcohen/eslint-plugin-jsx-a11y/blob/master/docs/rules/aria-role.md)

    ```jsx
    // kötü - bir ARIA role değil
    <div role="datepicker" />

    // kötü - soyut ARIA role
    <div role="range" />

    // iyi
    <div role="button" />
    ```

  - Elemanlarde `accessKey` kullanmayın. eslint: [`jsx-a11y/no-access-key`](https://github.com/evcohen/eslint-plugin-jsx-a11y/blob/master/docs/rules/no-access-key.md)

  > Neden? Ekran okuyucu kullanan insanlar tarafından kullanılan klavye komutları ve klavye kısayolları ile klavyeler arasındaki tutarsızlıkar erişilebilirliği zorlaştırmaktadır.

  ```jsx
  // bad
  <div accessKey="h" />

  // good
  <div />
  ```

  - `key` özelliği için dizinin index değerini kullanmaktan kaçının; benzersiz bir ID tercih edin. ([why?](https://medium.com/@robinpokorny/index-as-a-key-is-an-anti-pattern-e0349aece318))

  ```jsx
  // kötü
  {todos.map((todo, index) =>
    <Todo
      {...todo}
      key={index}
    />
  )}

  // iyi
  {todos.map(todo => (
    <Todo
      {...todo}
      key={todo.id}
    />
  ))}
  ```

  - Always define explicit defaultProps for all non-required props.

  > Why? propTypes are a form of documentation, and providing defaultProps means the reader of your code doesn’t have to assume as much. In addition, it can mean that your code can omit certain type checks.

  ```jsx
  // bad
  function SFC({ foo, bar, children }) {
    return <div>{foo}{bar}{children}</div>;
  }
  SFC.propTypes = {
    foo: PropTypes.number.isRequired,
    bar: PropTypes.string,
    children: PropTypes.node,
  };

  // good
  function SFC({ foo, bar, children }) {
    return <div>{foo}{bar}{children}</div>;
  }
  SFC.propTypes = {
    foo: PropTypes.number.isRequired,
    bar: PropTypes.string,
    children: PropTypes.node,
  };
  SFC.defaultProps = {
    bar: '',
    children: null,
  };
  ```

## Refler

  - ref değeri için her zaman callback kullanın. eslint: [`react/no-string-refs`](https://github.com/yannickcr/eslint-plugin-react/blob/master/docs/rules/no-string-refs.md)

    ```jsx
    // kötü
    <Foo
      ref="myRef"
    />

    // iyi
    <Foo
      ref={(ref) => { this.myRef = ref; }}
    />
    ```

## Parantezler

  - JSX etiketleri bir satırdan fazla olursa parantezler kullanın. eslint: [`react/jsx-wrap-multilines`](https://github.com/yannickcr/eslint-plugin-react/blob/master/docs/rules/jsx-wrap-multilines.md)

    ```jsx
    // kötü
    render() {
      return <MyComponent className="long body" foo="bar">
               <MyChild />
             </MyComponent>;
    }

    // iyi
    render() {
      return (
        <MyComponent className="long body" foo="bar">
          <MyChild />
        </MyComponent>
      );
    }

    // iyi, eğer tek satırsa
    render() {
      const body = <div>hello</div>;
      return <MyComponent>{body}</MyComponent>;
    }
    ```

## Taglar

  - Hiçbir çocuğu olmayan etiketleri her zaman kendi kendine kapatın. eslint: [`react/self-closing-comp`](https://github.com/yannickcr/eslint-plugin-react/blob/master/docs/rules/self-closing-comp.md)

    ```jsx
    // kötü
    <Foo className="stuff"></Foo>

    // iyi
    <Foo className="stuff" />
    ```

  - Eğer bileşeninizin birden çok satırdan oluşan özellikleri varsa, bileşeni yeni bir satırda kapatın. eslint: [`react/jsx-closing-bracket-location`](https://github.com/yannickcr/eslint-plugin-react/blob/master/docs/rules/jsx-closing-bracket-location.md)

    ```jsx
    // kötü
    <Foo
      bar="bar"
      baz="baz" />

    // iyi
    <Foo
      bar="bar"
      baz="baz"
    />
    ```

## Metodlar

  - Yerel değişkenleri kapatmak için ok fonksiyonları kullanın.

    ```jsx
    function ItemList(props) {
      return (
        <ul>
          {props.items.map((item, index) => (
            <Item
              key={item.key}
              onClick={() => doSomethingWith(item.name, index)}
            />
          ))}
        </ul>
      );
    }
    ```

  - Render metodunda kullanılacak olay yöneticilerini constructor metodunda bağlayın. eslint: [`react/jsx-no-bind`](https://github.com/yannickcr/eslint-plugin-react/blob/master/docs/rules/jsx-no-bind.md)

    > Neden? Render metodunda yer alan bir bağlama çağrısı her render işleminde tamamen yeni bir fonksiyon yaratır.

    ```jsx
    // kötü
    class extends React.Component {
      onClickDiv() {
        // bişeyler yap
      }

      render() {
        return <div onClick={this.onClickDiv.bind(this)} />;
      }
    }

    // iyi
    class extends React.Component {
      constructor(props) {
        super(props);

        this.onClickDiv = this.onClickDiv.bind(this);
      }

      onClickDiv() {
        // bişeyler yap
      }

      render() {
        return <div onClick={this.onClickDiv} />;
      }
    }
    ```

  - React bileşeninin dahili metodları için alt çizgi önekini kullanmayın.
    > Neden? Alt çizgi önekleri gizliliği belirtmek için diğer dillerde bir kural olarak bazen kullanılır. Fakat, bu dillerin aksine, JavaScript'de gizlilik için doğal bir destek yok, her şey açıktır. Niyetlerinize bakılmaksızın, property lere alt çizgi önekleri eklemek onları aslında gizli kılmaz ve herhangi bir property (alt çizgi öneki içeren veya içermeyen) herkese açık olarak değerlendirilmelidir. Daha detaylı bir tartışma için [#1024](https://github.com/airbnb/javascript/issues/1024) ve [#490](https://github.com/airbnb/javascript/issues/490) sorun sayfalarına göz atın.

    ```jsx
    // kötü
    React.createClass({
      _onClickSubmit() {
        // bişeyler yap
      },

      // diğer şeyler
    });

    // iyi
    class extends React.Component {
      onClickSubmit() {
        // bişeyler yap
      }

      // diğer şeyler
    }
    ```

  - `render` metodunda bir değer döndürdüğünüze emin olun. eslint: [`react/require-render-return`](https://github.com/yannickcr/eslint-plugin-react/blob/master/docs/rules/require-render-return.md)

    ```jsx
    // kötü
    render() {
      (<div />);
    }

    // iyi
    render() {
      return (<div />);
    }
    ```

## Sıralama

  - `class extends React.Component` için sıralama:

  1. optional `static` methods
  1. `constructor`
  1. `getChildContext`
  1. `componentWillMount`
  1. `componentDidMount`
  1. `componentWillReceiveProps`
  1. `shouldComponentUpdate`
  1. `componentWillUpdate`
  1. `componentDidUpdate`
  1. `componentWillUnmount`
  1. *clickHandlers or eventHandlers* like `onClickSubmit()` or `onChangeDescription()`
  1. *getter methods for `render`* like `getSelectReason()` or `getFooterContent()`
  1. *optional render methods* like `renderNavigation()` or `renderProfilePicture()`
  1. `render`

  - `propTypes`, `defaultProps`, `contextTypes`, vs. nasıl tanımlanır:

    ```jsx
    import React from 'react';
    import PropTypes from 'prop-types';

    const propTypes = {
      id: PropTypes.number.isRequired,
      url: PropTypes.string.isRequired,
      text: PropTypes.string,
    };

    const defaultProps = {
      text: 'Hello World',
    };

    class Link extends React.Component {
      static methodsAreOk() {
        return true;
      }

      render() {
        return <a href={this.props.url} data-id={this.props.id}>{this.props.text}</a>;
      }
    }

    Link.propTypes = propTypes;
    Link.defaultProps = defaultProps;

    export default Link;
    ```

  - `React.createClass` için sıralama: eslint: [`react/sort-comp`](https://github.com/yannickcr/eslint-plugin-react/blob/master/docs/rules/sort-comp.md)

  1. `displayName`
  1. `propTypes`
  1. `contextTypes`
  1. `childContextTypes`
  1. `mixins`
  1. `statics`
  1. `defaultProps`
  1. `getDefaultProps`
  1. `getInitialState`
  1. `getChildContext`
  1. `componentWillMount`
  1. `componentDidMount`
  1. `componentWillReceiveProps`
  1. `shouldComponentUpdate`
  1. `componentWillUpdate`
  1. `componentDidUpdate`
  1. `componentWillUnmount`
  1. *clickHandlers or eventHandlers* like `onClickSubmit()` or `onChangeDescription()`
  1. *getter methods for `render`* like `getSelectReason()` or `getFooterContent()`
  1. *optional render methods* like `renderNavigation()` or `renderProfilePicture()`
  1. `render`

## `isMounted`

  - `isMounted` kullanmayın. eslint: [`react/no-is-mounted`](https://github.com/yannickcr/eslint-plugin-react/blob/master/docs/rules/no-is-mounted.md)

  > Neden? [`isMounted` düzene uygun değildir][anti-pattern], ES6 sınıflarını kullanırken geçerli değildir, ve resmi olarak kaldırılmak için üzeredir.

  [anti-pattern]: https://facebook.github.io/react/blog/2015/12/16/ismounted-antipattern.html

## Çeviri

  Bu JSX/React stil rehberi aynı zamanda diğer dillerde de mevcuttur:

  - ![cn](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/China.png) **Chinese (Simplified)**: [JasonBoy/javascript](https://github.com/JasonBoy/javascript/tree/master/react)
  - ![tw](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Taiwan.png) **Chinese (Traditional)**: [jigsawye/javascript](https://github.com/jigsawye/javascript/tree/master/react)
  - ![es](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Spain.png) **Español**: [agrcrobles/javascript](https://github.com/agrcrobles/javascript/tree/master/react)
  - ![jp](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Japan.png) **Japanese**: [mitsuruog/javascript-style-guide](https://github.com/mitsuruog/javascript-style-guide/tree/master/react)
  - ![kr](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/South-Korea.png) **Korean**: [apple77y/javascript](https://github.com/apple77y/javascript/tree/master/react)
  - ![pl](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Poland.png) **Polish**: [pietraszekl/javascript](https://github.com/pietraszekl/javascript/tree/master/react)
  - ![Br](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Brazil.png) **Portuguese**: [ronal2do/javascript](https://github.com/ronal2do/airbnb-react-styleguide)
  - ![ru](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Russia.png) **Russian**: [leonidlebedev/javascript-airbnb](https://github.com/leonidlebedev/javascript-airbnb/tree/master/react)
  - ![th](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Thailand.png) **Thai**: [lvarayut/javascript-style-guide](https://github.com/lvarayut/javascript-style-guide/tree/master/react)
  - ![tr](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Turkey.png) **Turkish**: [alioguzhan/react-style-guide](https://github.com/alioguzhan/react-style-guide)
  - ![ua](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Ukraine.png) **Ukrainian**: [ivanzusko/javascript](https://github.com/ivanzusko/javascript/tree/master/react)

**[⬆ back to top](#içindekiler)**

