<template>
  <div class="categories">
    <button @click="filterProducts('Монобукеты')">
      <img src="https://cdn-icons-png.flaticon.com/512/1261/1261146.png" alt="Монобукеты" class="category-icon" />
      <span>Монобукеты</span>
    </button>
    <button @click="filterProducts('Авторские букеты')">
      <img src="https://cdn-icons-png.flaticon.com/512/2438/2438198.png" alt="Авторские букеты" class="category-icon"/>
      <span>Авторские букеты</span>
    </button>
    <button @click="filterProducts('Букеты в корзинках')">
      <img src="https://cdn-icons-png.flaticon.com/512/4148/4148023.png" alt="Букеты в корзинках" class="category-icon"/>
      <span>Букеты в корзинках</span>
    </button>
  </div>
  <div class="product-list">
    <div class="product-item" v-for="product in displayedProducts" :key="product.id">
      <img src="../assets/rose.jpg" alt="Product Image" class="product-image"/>
      <div class="product-details">
        <h3>{{ product.name }}</h3>
        <p>{{ product.description }}</p>
        <p>Цена: {{ product.price }} руб.</p>
        <div class="quantity-controls">
          <button @click="removeFromCart(product.id)">-</button>
          <span>{{getProductQuantity(product.id) || 0}}</span>
          <button @click="addToCart(product.id)">+</button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { mapState } from 'vuex';
import flowerData from "@/assets/flowers.json";
export default {
  data() {
    return {
      products: flowerData,
      cart:[],
    };
  },
  methods: {
    addToCart(itemId) {
      const item = this.products.find(({ id }) => id === itemId);
      if (!localStorage.getItem("cart")) {
        localStorage.setItem("cart", JSON.stringify([]));
      }
      const cartItems = JSON.parse(localStorage.getItem("cart"));
      const existingItem = cartItems.find(cartItem => cartItem.id === itemId);

      if (existingItem) {
        // Если товар уже в корзине, увеличиваем количество
        existingItem.quantity += 1;
      } else {
        // Если товара нет в корзине, добавляем новый элемент с начальным количеством 1
        item.quantity = 1;
        cartItems.push(item);
      }

      // Обновляем количество товаров в локальном хранилище
      localStorage.setItem("cart", JSON.stringify(cartItems));
      this.cart = JSON.parse(localStorage.getItem("cart"));
    },
    removeFromCart(itemId) {
      const cartItems = JSON.parse(localStorage.getItem("cart"));
      const index = cartItems.findIndex(({ id }) => id === itemId);

      if (index !== -1) {
        const existingItem = cartItems[index];
        if (existingItem.quantity > 1) {
          // Если у товара количество больше 1, уменьшаем количество
          existingItem.quantity -= 1;
        } else {
          // Если у товара количество равно 1 или меньше, удаляем его из корзины
          cartItems.splice(index, 1);
        }

        // Обновляем количество товаров в локальном хранилище
        localStorage.setItem("cart", JSON.stringify(cartItems));
        this.cart = JSON.parse(localStorage.getItem("cart"));
      }
    },
    getProductQuantity(itemId) {
      const cartItems = JSON.parse(localStorage.getItem("cart")) || [];
      const item = cartItems.find(({ id }) => id === itemId);
      return item ? item.quantity : 0;
    },
  },
  computed: {
    ...mapState(['searchText']),
    displayedProducts() {
      if (this.searchText.trim() === '') {
        return this.products; // Показываем все продукты, если поле поиска пусто
      } else {
        // Используем метод filter для отображения только результатов поиска
        return this.products.filter(product =>
            product.name.toLowerCase().includes(this.searchText.toLowerCase()) ||
            product.description.toLowerCase().includes(this.searchText.toLowerCase())
        );
      }
    },
  },
};
</script>

<style>
.categories {
  display: flex;
  justify-content: space-around;
  margin: 20px 0;
}

.categories button {
  display: flex;
  flex-direction: column;
  align-items: center;
  cursor: pointer;
  background: none;
  border: none;
}

.category-icon {
  width: 50%; /* Установите желаемую ширину и высоту для иконки */
  height: 50%;
}

.product-list {
  display: flex;
  flex-wrap: wrap;
  justify-content: space-around;
  overflow: hidden;
}

.product-item {
  width: calc(33.33% - 20px); /* Уменьшено расстояние между карточками */
  background-color: black;
  border-radius: 5px;
  box-shadow: 0 2px 5px rgba(0, 0, 0, 0.1);
  margin: 0 0 20px 0;
  transition: transform 0.3s;
  overflow: hidden; /* Обрезать изображение, если оно не соответствует карточке */
}

.product-item:hover {
  transform: translateY(-5px);
}

.product-image {
  width: 100%;
  height: auto;
  border-top-left-radius: 5px;
  border-top-right-radius: 5px;
}

.product-details {
  padding: 15px;
}

/* Добавлен стиль для кнопки "Добавить в корзину" (может быть изменен в зависимости от ваших потребностей) */
.product-item button {
  background-color: blue;
  color: white;
  padding: 8px 12px;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  transition: background-color 0.3s;
}

.product-item button:hover {
  background-color: darkblue;
}

@media screen and (max-width: 768px) {
  .product-item {
    width: calc(50% - 20px); /* Для экранов с шириной до 768px, уменьшаем количество карточек в ряду */
  }
}

::-webkit-scrollbar {
  width: 0; /* Ширина полосы прокрутки */
}

 .quantity-controls {
   display: flex;
   align-items: center;
   justify-content: center;
   margin-top: 10px;
 }

.quantity-controls button {
  background-color: #333333;
  border: none;
  border-radius: 50%;
  width: 30px;
  height: 30px;
  font-size: 16px;
  margin: 0 5px;
  cursor: pointer;
  transition: background-color 0.3s;
}

.quantity-controls button:hover {
  background-color: #ccc;
}

.quantity-controls span {
  margin: 0 5px;
}
</style>

