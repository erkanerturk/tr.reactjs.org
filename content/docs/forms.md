---
id: forms
title: Forms
permalink: docs/forms.html
prev: lists-and-keys.html
next: lifting-state-up.html
redirect_from:
  - "tips/controlled-input-null-value.html"
  - "docs/forms-zh-CN.html"
---

HTML form elemanları, React’te diğer DOM elemanlarından biraz farklı çalışır, çünkü form elemanlarının kendilerine has iç state'leri vardır. Örneğin, bu kod HTML’de bir form içerisinde name girişi ister:

```html
<form>
  <label>
    Name:
    <input type="text" name="name" />
  </label>
  <input type="submit" value="Submit" />
</form>
```

Bu form, kullanıcı formu gönderdiğinde yeni bir sayfaya göz atmak gibi varsayılan bir HTML formu davranışına sahiptir. Bu davranışı React'te istiyorsan, işe yarıyor. Ancak çoğu durumda, formun gönderimini işleyen ve kullanıcının forma girdiği verilere erişen bir JavaScript işlevine sahip olmak uygundur. Bunu başarmanın standart yolu "kontrollü bileşenler" adı verilen bir tekniktir.

## Kontrollü Bileşenler {#controlled-components}

HTML’de, `<input>`, `<textarea>` ve `<select>` gibi form elemanları genellikle kendi state’ini korur ve kullanıcı girdisine dayalı olarak güncellenir. React’te ise state’ler genellikle bileşenlerin this.state özelliğinde saklanır ve yalnızca  [`setState()`](/docs/react-component.html#setstate). ile güncellenir.

React state’te tek kaynak olarak ikisini birleştirebiliriz. Ardından form oluşturan React bileşeni, sonraki kullanıcı girişi üzerinde bu formda olanı da kontrol eder. Değeri React tarafından bu şekilde kontrol edilen bir giriş form elemanına kontrollü bileşen denir.

Örneğin, bir önceki örnekte, name değerinin yazılıp submit edildiğinde name i alert ile yazdırmak istiyorsak, formu kontrollü bir bileşen olarak oluşturabiliriz:

```javascript{4,10-12,24}
class NameForm extends React.Component {
  constructor(props) {
    super(props);
    this.state = {value: ''};

    this.handleChange = this.handleChange.bind(this);
    this.handleSubmit = this.handleSubmit.bind(this);
  }

  handleChange(event) {
    this.setState({value: event.target.value});
  }

  handleSubmit(event) {
    alert('A name was submitted: ' + this.state.value);
    event.preventDefault();
  }

  render() {
    return (
      <form onSubmit={this.handleSubmit}>
        <label>
          Name:
          <input type="text" value={this.state.value} onChange={this.handleChange} />
        </label>
        <input type="submit" value="Submit" />
      </form>
    );
  }
}
```

[**CodePen'de Deneyin**](https://codepen.io/gaearon/pen/VmmPgp?editors=0010)

`value` özelleği input’un kendisinde zaten var. Öyleyse bu değeri almak için yeni bir React state’i oluşturmaya gerek yok. Bu inputta `value` olarak state’i yazdıracağız ve input’ta her değişiklik olduğunda bu state’i güncelleyeceğiz.

Kontrollü bir bileşende her state değişimi, `handleChange` fonksiyonunu çalıştıracaktır. Örneğin, adın büyük harflerle yazılmasını isteseydik, `handleChange` fonksiyonunu şu şekilde yazabilirdik:

```javascript{2}
handleChange(event) {
  this.setState({value: event.target.value.toUpperCase()});
}
```

## Textarea Elemanı {#the-textarea-tag}

HTML’de, `<textarea>` elemanı yazıyı çocuğunda tanımlar:

```html
<textarea>
  Merhaba, burası textarea yazı alanıdır.
</textarea>
```

Bunun yerine React, `<textarea>` için bir `value` özelliği kullanır. Bu şekilde `<textarea>` kullanan bir form, tek satırlı bir girdi kullanan bir forma çok benzer şekilde yazılabilir:

```javascript{4-6,12-14,26}
class EssayForm extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      value: 'Bu kısma bir şeyler yazınız.'
    };

    this.handleChange = this.handleChange.bind(this);
    this.handleSubmit = this.handleSubmit.bind(this);
  }

  handleChange(event) {
    this.setState({value: event.target.value});
  }

  handleSubmit(event) {
    alert('Gönderilen değer: ' + this.state.value);
    event.preventDefault();
  }

  render() {
    return (
      <form onSubmit={this.handleSubmit}>
        <label>
          Essay:
          <textarea value={this.state.value} onChange={this.handleChange} />
        </label>
        <input type="submit" value="Gönder" />
      </form>
    );
  }
}
```

`this.state.value` 'in constructor’da başlatıldığına dikkat edin, böylece textarea içerisinde varsayılan olarak bu yazı bulunacaktır

## Select Elemanı {#the-select-tag}

HTML’de `<select>`, bir açılır liste oluşturur. Örneğin, aşağıdaki kod bazı meyveleri listeler:

```html
<select>
  <option value="elma">Elma</option>
  <option value="armut">Armut</option>
  <option selected value="havuç">Havuç</option>
  <option value="muz">Muz</option>
</select>
```

`Havuç` seçeneğinin başlangıçta `selected` özelliği yüzünden seçili olarak geleceğini unutmayın. React, bu `selected` özelliğini kullanmak yerine, `select` etiketinde bir `value` özelliği kullanır. Kontrollü bir bileşende bu daha kullanışlıdır çünkü yalnızca bir yerde güncelleme yapmanızı sağlar. Örneğin:

```javascript{4,10-12,24}
class FlavorForm extends React.Component {
  constructor(props) {
    super(props);
    this.state = {value: 'havuç'};

    this.handleChange = this.handleChange.bind(this);
    this.handleSubmit = this.handleSubmit.bind(this);
  }

  handleChange(event) {
    this.setState({value: event.target.value});
  }

  handleSubmit(event) {
    alert('Favori meyveniz: ' + this.state.value);
    event.preventDefault();
  }

  render() {
    return (
      <form onSubmit={this.handleSubmit}>
        <label>
          Favori meyveni seç:
          <select value={this.state.value} onChange={this.handleChange}>
            <option value="elma">Elma</option>
            <option value="armut">Armut</option>
            <option value="havuç">Havuç</option>
            <option value="muz">Muz</option>
          </select>
        </label>
        <input type="submit" value="Gönder" />
      </form>
    );
  }
}
```

[**CodePen'de Deneyin**](https://codepen.io/gaearon/pen/JbbEzX?editors=0010)

Genel olarak bu, `<input type="text">`, `<textarea>` ve `<select>` elementlerinin çok benzer şekilde çalışmasını sağlar.

> Not
>
> Bir `select` etiketinde birden fazla seçeneği seçmenize izin veren bir diziyi `value` attribute’üne yazabilirsiniz:
>
>```js
><select multiple={true} value={['B', 'C']}>
>```

## Dosya Girişi Elemanı {#the-file-input-tag}

HTML'de bir `<input type="file">` elemanı, kullanıcını cihazının depolama alanından bir veya daha fazla dosyayı sunucuya yüklemesini ya da JavaScript'in [File API](https://developer.mozilla.org/en-US/docs/Web/API/File/Using_files_from_web_applications) aracılığıyla manipüle etmesini sağlar.

```html
<input type="file" />
```

Değeri salt okunur olduğu için, React'te **kontrolsüz** bir bileşendir. Daha sonra diğer kontrol edilemeyen bileşenlerle birlikte [dokümanlarda](/docs/uncontrolled-components.html#the-file-input-tag) ele alınmıştır.


## Çoklu Girişleri Ele Alma {#handling-multiple-inputs}

Çoklu kontrollü `input` öğelerini ele almanız gerektiğinde, her öğeye bir `name` özniteliği ekleyebilir ve işleyici işlevinin `event.target.name` değerine dayanarak ne yapılacağını seçmesine izin verebilirsiniz.

Örneğin:

```javascript{15,18,28,37}
class Rezervasyon extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      gidiyor: true,
      ziyaretciSayisi: 2
    };

    this.handleInputChange = this.handleInputChange.bind(this);
  }

  handleInputChange(event) {
    const target = event.target;
    const value = target.type === 'checkbox' ? target.checked : target.value;
    const name = target.name;

    this.setState({
      [name]: value
    });
  }

  render() {
    return (
      <form>
        <label>
          Gidiyor:
          <input
            name="gidiyor"
            type="checkbox"
            checked={this.state.gidiyor}
            onChange={this.handleInputChange} />
        </label>
        <br />
        <label>
          Misafir Sayısı:
          <input
            name="ziyaretciSayisi"
            type="number"
            value={this.state.ziyaretciSayisi}
            onChange={this.handleInputChange} />
        </label>
      </form>
    );
  }
}
```

[**CodePen'de Deneyin**](https://codepen.io/gaearon/pen/wgedvV?editors=0010)

Verilen girdi ismine karşılık gelen state keyini güncellemek için [ES6 syntax](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Operators/Object_initializer#Computed_property_names)’ını nasıl kullandığımıza dikkat edin:

```js{2}
this.setState({
  [name]: value
});
```

Bu da ES5’teki eşdeğer kodudur.

```js{2}
var partialState = {};
partialState[name] = value;
this.setState(partialState);
```
Ayrıca, `setState()` otomatik olarak [kısmi bir durumu geçerli duruma birleştirir](/docs/state-and-lifecycle.html#state-updates-are-merged) olduğundan, yalnızca değiştirilen parçalarla çağırmamız gerekiyor.

## Kontrollü Giriş Boş Değer {#controlled-input-null-value}

[Kontrollü bir component](/docs/forms.html#controlled-components) üzerindeki props’u belirlemek, kullanıcının isteği dışında girişi değiştirmesini önler. `value` belirttiyseniz ancak girdi hala düzenlenebilir ise, yanlışlıkla `value`'i `undefined` veya `null` olarak ayarlamış olabilirsiniz.

Aşağıdaki kod bunu göstermektedir. (Giriş ilk önce kilitlenir ancak kısa bir gecikme sonrasında düzenlenebilir hale gelir.)

```javascript
ReactDOM.render(<input value="selam" />, mountNode);

setTimeout(function() {
  ReactDOM.render(<input value={null} />, mountNode);
}, 1000);

```

## Kontrollü Bileşenlere Alternatifler {#alternatives-to-controlled-components}

Kontrollü bileşenleri kullanmak bazen sıkıcı olabilir, çünkü verilerinizin bir React bileşeniyle tüm giriş durumunu değiştirebilmesi ve yayınlayabilmesi için bir olay işleyicisi yazmanız gerekir. Bu, önceden var olan bir kod tabanını React'e dönüştürürken veya bir React uygulamasını React olmayan bir kütüphaneyle birleştirirken özellikle can sıkıcı olabilir. Bu durumlarda, giriş formlarını uygulamak için alternatif bir teknik olan [kontrolsüz bileşenler](/docs/uncontrolled-components.html) 'i kontrol etmek isteyebilirsiniz.

## Tam Teşekküllü Çözümler {#fully-fledged-solutions}

Doğrulama, ziyaret edilen alanları takip etme ve form teslimini içeren eksiksiz bir çözüm arıyorsanız [Formik](https://jaredpalmer.com/formik) popüler seçeneklerden biridir. Bununla birlikte, aynı kontrol bileşenlerinin ve yönetim durumunun aynı ilkeleri üzerine kuruludur - bu yüzden bunları öğrenmeyi ihmal etmeyin.
