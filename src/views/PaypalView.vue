<template>
  <div>


    

    <div v-if="!paidFor">
      <h1>{{ products.nomeProduto }}</h1>
      <h1>{{ products.preco_produto + ' R$' }}</h1>

      <p>{{ product.description }}</p>

    </div>

    <div v-if="paidFor">
      <h1>Noice, you bought a beautiful lamp!</h1>
    </div>

    <div ref="paypal"></div>

    
    
  </div>

  
</template>
  
<script>
import axios from 'axios'

// import image from "../assets/lamp.png"
export default {
  name: "HelloWorld",

  data() {
    return {
      products: [],
      loaded: false,
      paidFor: false,
      product: {
        price: 777.77,
        description: "leg lamp from that one movie",
        img: "./assets/lamp.jpg"
      }
    }
  },

  // data: function () {
  //   return {
  //     loaded: false,
  //     paidFor: false,
  //     product: {
  //       price: 777.77,
  //       description: "leg lamp from that one movie",
  //       img: "./assets/lamp.jpg"
  //     }
  //   };
  // },
  mounted: function () {
    const id = this.$route.params.id
    if (id) {
      let username = 'robson.flavio'
      let password = 'senha123'
      axios.get(
        `http://localhost:8080/api/user-products/${id}`,
        {
          auth: {
            username: username,
            password: password
          },
        })
        .then(resp => { this.products = resp.data, console.log(resp.data) })
        .catch(error => {
          alert(error)
          this.$router.push("/bolo")
        })
    }



    const script = document.createElement("script");
    script.src =
      "https://www.paypal.com/sdk/js?client-id=AZrNyxZs1wtJqybBFEJonpJEGOVwRaxDkcWmPCVN0oDhhFIir7rfDnouS3Y6VG79Uvna2Og0ECRe4hBx";
    script.addEventListener("load", this.setLoaded);
    document.body.appendChild(script);
  },
  methods: {
    setLoaded: function () {
      this.loaded = true;
      window.paypal
        .Buttons({
          createOrder: (data, actions) => {
            return actions.order.create({
              purchase_units: [
                {
                  description: this.products.descProduto,
                  amount: {
                    currency_code: "USD",
                    value: this.products.preco_produto
                  }
                }
              ]
            });
          },
          onApprove: async (data, actions) => {
            const order = await actions.order.capture();
            this.paidFor = true;
            console.log(order);
          },
          onError: err => {
            console.log(err);
          }
        })
        .render(this.$refs.paypal);
    }
  }
};
</script>
<style>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

h1 {
  text-align: center;
  margin: 1em 0;
  font-family: sans-serif;
}

.t {
  border: 1px solid
}



.card-group {
  border: 1px solid red;
  height: auto;
  /* alterar para 500px caso os cards fiquem de tamanhos diferentes */
  width: 300px;
  padding: 15px;

  float: left;
}



.card-img-top {
  padding: 3px;
}
</style>