<template>
<div id="root">
  <div class="container mt-5">
    <div class="row">
      <div class="col-md-4">
        <div class="list-group">
          <a v-for="drink in data" :key="drink.id" href="#" class="list-group-item list-group-item-action" 
            @click.prevent="addToCart(drink)">
            <div class="d-flex w-100 justify-content-between">
                <h5 class="mb-1">{{drink.name}}</h5>
                <small>{{ drink.price }}</small>
                <!-- <small>$50</small> -->
            </div>
            <p class="mb-1">{{drink.description}}</p>
          </a>
        </div>
      </div>
      <div class="col-md-8">

        <!-- 購物車 -->
        <table class="table">
          <thead>
            <tr>
              <th scope="col" width="50">操作</th>
              <th scope="col">品項</th>
              <th scope="col">描述</th>
              <th scope="col" width="90">數量</th>
              <th scope="col">單價</th>
              <th scope="col">小計</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="drink in cart" :key="drink.id">
              <!-- 刪除按鈕 -->
              <td><button type="button" class="btn btn-sm" @click="removeFromCart(drink.id)">x</button></td>
              <td>{{drink.name}}</td>
              <td><small>{{drink.description}}</small></td>
              <!-- 數量選擇 -->
              <td>
                <select class="form-select" v-model="drink.quantity" @click="updateCart(drink)">
                  <option v-for="n in 10" :key="n" :value="n" >{{ n }}</option>
                  <!-- <option value="1">1</option>
                  <option value="2">2</option>
                  <option value="3">3</option>
                  <option value="4">4</option>
                  <option value="5">5</option>
                  <option value="6">6</option>
                  <option value="7">7</option>
                  <option value="8">8</option>
                  <option value="9">9</option>
                  <option value="10">10</option> -->
                </select>
              </td>
              <!-- 單價 -->
              <td>{{drink.price}}</td>
              <!-- 小計 -->
              <td>{{ itemSubtotal(drink) }}</td>
            </tr>
          </tbody>
        </table>

        <!-- 計算總金額 -->
        <div class="text-end mb-3">
          <h5>總計: <span>${{ total }}</span></h5>
          <!-- <h5>總計: <span>$100</span></h5> -->
        </div>
        <textarea
          class="form-control mb-3"
          rows="3"
          placeholder="備註"
          v-model="description"
        ></textarea>
        
        <!-- 提交 -->
        <div class="text-end">
          <button class="btn btn-primary" @click="createOrder">送出</button>
        </div>
      </div>
    </div>
    <hr />

    <!-- 訂單 -->
    <div class="row justify-content-center">
      <div class="col-8">
        <div class="card">
          <div class="card-body">
            <div class="card-title">
              <h5>訂單</h5>
              <table class="table">
                <thead>
                  <tr>
                    <th scope="col">品項</th>
                    <th scope="col">數量</th>
                    <th scope="col">小計</th>
                  </tr>
                </thead>
                <tbody>
                  <tr v-for="item in order.cart" :key="item.id">
                    <td>{{ item.name }}</td>
                    <td>{{ item.quantity }}</td>
                    <td>{{ itemSubtotal(item) }}</td>
                  </tr>
                  <!-- <tr>
                    <td>冬瓜檸檬</td>
                    <td>7</td>
                    <td>315</td>
                  </tr>
                  <tr>
                    <td>冬瓜檸檬</td>
                    <td>4</td>
                    <td>180</td>
                  </tr> -->
                </tbody>
              </table>
              <div class="text-end">備註: <span>{{ order.description }}</span></div>
              <!-- <div class="text-end">備註: <span>都不要香菜</span></div> -->
              <div class="text-end">
                <h5>總計: <span>${{ order.sum }}</span></h5>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</div>
</template>

<script setup>
import { reactive, ref, computed } from 'vue';
const data = [
  {
    id: 1,
    name: '珍珠奶茶',
    description: '香濃奶茶搭配QQ珍珠',
    price: 50,
  },
  {
    id: 2,
    name: '冬瓜檸檬',
    description: '清新冬瓜配上新鮮檸檬',
    price: 45,
  },
  {
    id: 3,
    name: '翡翠檸檬',
    description: '綠茶與檸檬的完美結合',
    price: 55,
  },
  {
    id: 4,
    name: '四季春茶',
    description: '香醇四季春茶，回甘無比',
    price: 45,
  },
  {
    id: 5,
    name: '阿薩姆奶茶',
    description: '阿薩姆紅茶搭配香醇鮮奶',
    price: 50,
  },
  {
    id: 6,
    name: '檸檬冰茶',
    description: '檸檬與冰茶的清新組合',
    price: 45,
  },
  {
    id: 7,
    name: '芒果綠茶',
    description: '芒果與綠茶的獨特風味',
    price: 55,
  },
  {
    id: 8,
    name: '抹茶拿鐵',
    description: '抹茶與鮮奶的絕配',
    price: 60,
  },
];

const drinks = ref(data);
const description = ref('');
const cart = ref([]);
const order = ref({});
const addToCart = (drink) => {
  cart.value.push({ 
    ...drink, 
    id: Date.now(),
    quantity: 1 
  });
  console.log(cart.value);
};

const updateCart = (item) => {
  // 在這裡可以處理更新購物車的邏輯
  item.value = item.quantity;
  console.log('更新購物車:', item);
}

const itemSubtotal = (item) => {
  return item.price * item.quantity;
}

const removeFromCart = (id) => {
  //→ 意思是：只保留那些 id 不等於傳進來的 id 的項目。
  //→ 換句話說，就是「排除掉要刪除的那一個」。
  cart.value = cart.value.filter(item => item.id !== id);
}

const total = computed(() => {
  return cart.value.reduce((sum, item) => sum + item.price * item.quantity, 0);
});

const createOrder = () => {
  order.value = {
    id: Date.now(),
    cart: cart.value,
    description: description.value,
    sum: total.value,
  }
  cart.value = [];
  description.value = '';
  console.log('訂單已建立:', order.value);
  console.log('訂單已建立:', order.description);
}

</script>