# Atro-Sneakers
_______________
## Это полностью рабочий пример интернет магазина, разработанный с использованием JavaScript, HTML, SCSS, React.js

### Основная задача: Создание интернет-магазина кроссовок. 
### Цели: Воссоздать весь функцианал работы интернет-магазина, а именно:
#### 1) Подгрузку данных с удаленного сервера,
#### 2) Возможность добавления/удаления товара в/из "Корзина",
#### 3) Возможность добавления/удаления товара в/из "Избранное",
#### 4) Возможность оформления заказа.
_____________________________

## Предстояла непростая задача создания интернет-магазина, в этом проекте я решил использовать библиотеку React.js, React-router-dom, а также axios.
______
# Внешний вид сайта получился таковым
![site](https://sun9-36.userapi.com/impg/JVc2fUR6fuhanbq2CC4fNfdZytJ_AxNL-Xn2TA/Q-3ejI83B2A.jpg?size=1590x812&quality=96&sign=8852ef74abe517bc1439275047c24b79&type=album)

___
## Для простоты понимания структуры сайта, буду рассказывать по блокам:

### 1 Блок - App

```jsx
//В данном коде показаны использованные useState хуки
const [items, setItems] = React.useState([]);
  const [cartItems, setCartItems] = React.useState([]);
  const [favorites, setFavorites] = React.useState([]);
  const [searchValue, setSearchValue] = React.useState("");
  const [cartOpened, setCartOpened] = React.useState(false);
  const [isLoading, setIsLoading] = React.useState(true);

```
_____________________________________________
```jsx
// Также был использован хук useEffect для получения данных с удаленного сервера, обернутый в try-catch блок, без зависимости
 React.useEffect(() => {
    async function fetchData() {
      try {
        const [cartResponse, favoritesResponse, itemsResponse] =
          await Promise.all([
            axios.get(
              "https://6331cc103ea4956cfb67783b.mockapi.io/Cart-iteams"
            ),
            axios.get("https://6331cc103ea4956cfb67783b.mockapi.io/Favorite"),
            axios.get(
              "https://6331cc103ea4956cfb67783b.mockapi.io/Sneakers-iteams"
            ),
          ]);

        setIsLoading(false);
        setCartItems(cartResponse.data);
        setFavorites(favoritesResponse.data);
        setItems(itemsResponse.data);
      } catch (error) {
        alert("Ошибка при запросе данных ;(");
        console.error(error);
      }
    }

    fetchData();
  }, []);
```
_____
```jsx
// В этом коде представлена реализация добавления и проверки нахождения товара в "Корзина" с использованием
// parantId, так как при помещении товара в "Корзина" id добавленного товара генерируется автоматически и может не совпадать
// с id товара на главной страницы
const onAddToCart = async (obj) => {
    try {
      const findItem = cartItems.find(
        (item) => Number(item.parentId) === Number(obj.id)
      );
      if (findItem) {
        setCartItems((prev) =>
          prev.filter((item) => Number(item.parentId) !== Number(obj.id))
        );
        await axios.delete(
          `https://6331cc103ea4956cfb67783b.mockapi.io/Cart-iteams/${findItem.id}`
        );
      } else {
        setCartItems((prev) => [...prev, obj]);
        const { data } = await axios.post(
          "https://6331cc103ea4956cfb67783b.mockapi.io/Cart-iteams",
          obj
        );
        setCartItems((prev) =>
          prev.map((item) => {
            if (item.parentId === data.parentId) {
              return {
                ...item,
                id: data.id,
              };
            }
            return item;
          })
        );
      }
    } catch (error) {
      alert("Ошибка при добавлении в корзину");
      console.error(error);
    }
  };
```

```jsx
//В этом коде представлена реализация удаления товара из "Корзина"
 const onRemoveItem = (id) => {
    try {
      axios.delete(
        `https://6331cc103ea4956cfb67783b.mockapi.io/Cart-iteams/${id}`
      );
      setCartItems((prev) =>
        prev.filter((item) => Number(item.id) !== Number(id))
      );
    } catch (error) {
      alert("Ошибка при удалении из корзины");
      console.error(error);
    }
  };
}

```
## Реализация добавления/удаления товара в/из "Корзина" (1):
![cart-1](https://sun9-85.userapi.com/impg/XsDpBbLqZhWvC3kAbrMHxvhyiEgGeksOEJLajQ/mtIvEPhZLu4.jpg?size=1600x813&quality=96&sign=25b80ad2eadd28cb42052a236c158d6c&type=album)
## Реализация добавления/удаления товара в/из "Корзина" (2):
![cart-2](https://sun9-72.userapi.com/impg/2vm7b-4jYJSAwwWCb0_M1TPGv4HCenuBr4jiyw/ivYuGby7hy0.jpg?size=1607x813&quality=96&sign=39ea6adf2166c47ee938081810a271b0&type=album)
_______________
```jsx
// В этом коде представлена реализация добавления товара в "Избранное" обернутая в try-catch блок
 const onAddToFavorite = async (obj) => {
    try {
      if (favorites.find((favObj) => Number(favObj.id) === Number(obj.id))) {
        axios.delete(
          `https://6331cc103ea4956cfb67783b.mockapi.io/Favorite/${obj.id}`
        );
        setFavorites((prev) =>
          prev.filter((item) => Number(item.id) !== Number(obj.id))
        );
      } else {
        const { data } = await axios.post(
          "https://6331cc103ea4956cfb67783b.mockapi.io/Favorite",
          obj
        );
        setFavorites((prev) => [...prev, data]);
      }
    } catch (error) {
      alert("Не удалось добавить в фавориты");
      console.error(error);
    }
  };
```
# Добавление товара в "Избранное" осуществляется при нажатии на карточке товара кнопки "сердца", также переход в раздел "Избранное" осуществляется при нажатии в шапке сайта кнопки "сердца"
## Реализация добавления/удаления товара в/из "Избранное" (1):
![fav-1](https://sun9-87.userapi.com/impg/L9QNXPHxLlisdXOuuJZpkEBIIwZgNXbeJwvYLQ/KTmyACe3Dyc.jpg?size=1619x814&quality=96&sign=1f814a52a1f429458cc43762eb6d7d8b&type=album)
## Реализация добавления/удаления товара в/из "Избранное" (2):
![fav-2](https://sun9-80.userapi.com/impg/yUSejbPQnUg0f-xbaPt6ehLZYPlSSfhzrzWu7w/Z1kml_JmQHs.jpg?size=1613x809&quality=96&sign=a436eb8d2c0429599d254cdeb83cb92c&type=album)

```jsx
// Данная функция служит для проверки добавления товара
  const isItemAdded = (id) => {
    return cartItems.some((obj) => Number(obj.parentId) === Number(id));
  };
```
_______

# Также в проекте был использован useContext

```jsx
// Инициализация контекста в отдельном файле
import React from "react";

const AppContext = React.createContext({});

export default AppContext;
```
_________________________________

# Далее по свойствам react-router-dom были созданы следующие страницы: Home, Favorites, (Orders) - не доделана
_______
## HOME

```jsx
{
// Данная функция служит для рендера товаров на главной странице магазина
  const renderItems = () => {
    const filtredItems = items.filter((item) =>
      item.title.toLowerCase().includes(searchValue.toLowerCase())
    );
    return (isLoading ? [...Array(8)] : filtredItems).map((item, index) => (
      <Card
        key={index}
        onFavorite={(obj) => onAddToFavorite(obj)}
        onPlus={(item) => onAddToCart(item)}
        loading={isLoading}
        {...item}
      />
    ));
  };

```
## Данные берутся с удаленного сервера в формате JSON
## Это выглядит следующим образом 

```json
[{"id":1,"title":"Мужские кроссовки Nike Blazer Mid Suede","price":"12 999","imageUrl":"/img/sneakers/1.jpg"},{"id":2,"title":" Мужские кроссовки Nike Air Max 270","price":"12 999","imageUrl":"/img/sneakers/2.jpg"},{"id":3,"title":"Мужские кроссовки Nike Blazer Mid Suede","price":"8 499","imageUrl":"/img/sneakers/3.jpg"},{"id":4,"title":"Кроссовки Puma X Aka Boku Future Rider","price":"8 999","imageUrl":"/img/sneakers/4.jpg"},{"id":5,"title":"Мужские Кроссовки Under Armour Curry 8","price":"15 199","imageUrl":"/img/sneakers/5.jpg"},{"id":6,"title":" Мужские Кроссовки Nike Kyrie 7","price":"11 299","imageUrl":"/img/sneakers/6.jpg"},{"id":7,"title":"Мужские Кроссовки Jordan Air Jordan 11","price":"10 799","imageUrl":"/img/sneakers/7.jpg"},{"id":8,"title":"Мужские Кроссовки Nike LeBron XVIII","price":"16 999","imageUrl":"/img/sneakers/8.jpg"}]
```

## Сама карточка товара строится следующим образом 
```jsx
// Данный код показывает результат работы функцианального компонента Card
<div className={styles.card}>
      {loading ? (
        <ContentLoader
          speed={2}
          width={155}
          height={250}
          viewBox="0 0 155 265"
          backgroundColor="#f3f3f3"
          foregroundColor="#ecebeb"
        >
          <rect x="1" y="0" rx="10" ry="10" width="155" height="155" />
          <rect x="0" y="167" rx="5" ry="5" width="155" height="15" />
          <rect x="0" y="187" rx="5" ry="5" width="100" height="15" />
          <rect x="1" y="234" rx="5" ry="5" width="80" height="25" />
          <rect x="124" y="230" rx="10" ry="10" width="32" height="32" />
        </ContentLoader>
      ) : (
        <>
          {onFavorite && (
            <div className={styles.favorite} onClick={onClickFavorite}>
              <img
                src={isFavorite ? "img/liked.svg" : "img/unliked.svg"}
                alt="Unliked"
              />
            </div>
          )}
          <img width="100%" height={135} src={imageUrl} alt="Sneakers" />
          <h5>{title}</h5>
          <div className="d-flex justify-between align-center">
            <div className="d-flex flex-column">
              <span>Цена:</span>
              <b>{price} RUB.</b>
            </div>
            {onPlus && (
              <img
                className={styles.plus}
                onClick={onClickPlus}
                src={
                  isItemAdded(id) ? "img/btn-checked.svg" : "img/btn-plus.svg"
                }
                alt="Plus"
              />
            )}
          </div>
        </>
      )}
    </div>
```

## Карточка товара: 3 состояния
<img src="https://sun9-33.userapi.com/impg/ZnOn2I--i10O0JqLEMnXTuh0zjer_26hUsCAxA/n3PYAEa-EN4.jpg?size=278x382&quality=96&sign=c3d66c5bf7d161bf4c63d38564961b28&type=album" alt="card-1" width="200"/>
<img src="https://sun9-27.userapi.com/impg/aPNtxeTp8VGU4xa1p0SgTc5dgsahG1BS6eveSg/R3NCc7P4Un0.jpg?size=411x546&quality=96&sign=ec237467f30a1892f1b0fff06569597d&type=album" alt="card-2" width="200"/>
<img src="https://sun9-77.userapi.com/impg/SCuWi5scfG74je0_66xjzm--QmLQt5SsmRdQ9g/ESYRTxXM80M.jpg?size=398x534&quality=96&sign=bc74e47c6761943c3b689132d1c8574e&type=album" alt="card-3" width="200"/>

### Также товар однавременно может быть добавлен в "Корзина" и в "Избранное"
______

## FAVORITES:
### Устройство этой страницы схоже с устройством страницы home, поэтому просто приложу код
```jsx
// В данном коде показан результат работы функцианального-компонента Favorites
<div className="content p-40">
      <div className="d-flex align-center justify-between mb-40">
        <h1>Мои закладки</h1>
      </div>

      <div className="d-flex flex-wrap">
        {favorites.map((item, index) => (
          <Card
            key={index}
            favorited={true}
            onFavorite={onAddToFavorite}
            {...item}
          />
        ))}
      </div>
    </div>
```
____
## Drawer
### "Корзина" это отдельный функцианальный компонент, который можно использовать на любой из страниц
```jsx
// Этот код будет показан, если в "Корзина" добавлен товар
<div className="d-flex flex-column flex">
            <div className="items flex">
              {items.map((obj) => (
                <div
                  key={obj.id}
                  className="cartItem d-flex align-center mb-20"
                >
                  <div
                    style={{ backgroundImage: `url(${obj.imageUrl})` }}
                    className="cartItemImg"
                  ></div>

                  <div className="mr-20 flex">
                    <p className="mb-5">{obj.title}</p>
                    <b>{obj.price} руб.</b>
                  </div>
                  <img
                    onClick={() => onRemove(obj.id)}
                    className="removeBtn"
                    src="img/btn-remove.svg"
                    alt="Remove"
                  />
                </div>
              ))}
            </div>
            <div className="cartTotalBlock">
              <ul>
                <li>
                  <span>Итого:</span>
                  <div></div>
                  <b>{totalPrice} RUB. </b>
                </li>
                <li>
                  <span>Налог 5%:</span>
                  <div></div>
                  <b>{((totalPrice / 100) * 5).toFixed(2)} RUB. </b>
                </li>
              </ul>
              <button onClick={onClickOrder} className="greenButton">
                Оформить заказ <img src="img/arrow.svg" alt="Arrow" />
              </button>
            </div>
          </div>
          
//Этот блок будет показан, если в "Корзина" отсутствуют товары или был оформлен заказ
           <Info
            title={isOrderComplete ? "Заказ оформлен!" : "Корзина пустая"}
            description={
              isOrderComplete
                ? `Ваш заказ #${orderId} скоро будет передан курьерской доставке`
                : "Добавьте хотя бы одну пару кроссовок, чтобы сделать заказ."
            }
            image={
              isOrderComplete ? "img/complete-order.jpg" : "img/empty-cart.jpg"
            }
          />           
```

```jsx
// Данная функция реализует оформление заказа
const onClickOrder = async () => {
    setIsLoading(true);
    const { data } = await axios.post(
      "https://6331cc103ea4956cfb67783b.mockapi.io/Orders",
      {
        items: cartItems,
      }
    );
    setOrderId(data.id);
    setIsOrderComplete(true);
    setCartItems([]);

    for (let i = 0; i < cartItems.length; i++) {
      const item = cartItems[i];
      await axios.delete(
        "https://6331cc103ea4956cfb67783b.mockapi.io/Cart-iteams" + item.id
      );
      await delay(1000);
      setIsLoading(false);
    }
  };
```
## "Корзина": 3 состояния
<img src="https://sun9-49.userapi.com/impg/cfmqjd-11JilRR3uNVcpEbx2U-BoFtFh0PYmrQ/Q2aBJVuePeE.jpg?size=377x811&quality=96&sign=25fa60c8915b09782a2d823c7fadf2d9&type=album" alt="cart-1" width="200"/>
<img src="https://sun9-48.userapi.com/impg/GSDKNrfhVvFfYol1dGqb-mM0v4eaMNSNZPcKZA/CKd7VubrUrM.jpg?size=329x712&quality=96&sign=03c1a2f6bc1f3b0361df2c47dc70973b&type=album" alt="cart-2" width="200"/>
<img src="https://sun9-39.userapi.com/impg/0dXseQDf-STYHCp_AdjZMWoLMWiqeubW8WKj9A/IzspB7mWWLM.jpg?size=328x710&quality=96&sign=bed13bfdd1fb2066e5f39aecc1c65829&type=album" alt="cart-3" width="200"/>

### Также товар однавременно может быть добавлен в "Корзина" и в "Избранное"




______
# Бонусом был добален "Поиск" используя контролируемый input
```jsx
  const onChangeSearchInput = (event) => {
    setSearchValue(event.target.value);
  };
  
   <input
            onChange={onChangeSearchInput}
            value={searchValue}
            placeholder="Поиск..."
          />
```
## Его реализация выглядит следующим образом:
![search1](https://sun9-86.userapi.com/impg/pGgWrh4Ol5omYDd3voI1jTk-hmFLTK5pD32j7A/z-shYWXTt28.jpg?size=801x364&quality=96&sign=f01a9b021c3189477ea5fd67135528a9&type=album)

![search-2](https://sun9-82.userapi.com/impg/ILwMrVuRzttVZbDN0OIaONG3e298O0a2GYU7Ug/Iv96Qlvb31E.jpg?size=813x353&quality=96&sign=83d7dd91bd642a871aa4bb30eb55df11&type=album)
## Эти приложения показывают, что поиск по наименованию товара не зависит от регистра букв.

# На этом всё. Основные моменты были описаны!:smiley:
