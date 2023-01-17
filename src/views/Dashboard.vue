<template>


        <label>Server: <input v-model="server" type="text"></label>
        <br>
        <label>SID: <input v-model="sid" type="text"></label>
        <br>
        <button @click="getHangaar">Get Hangar</button>
        <button @click="getInventory">Get Inventory</button>

{{this.jsonData}}
<div v-if="mappedItems">
  <div class="py-4 container-fluid">
    <div class="row">
      <div class="col-lg-12">
        <div class="row">
       <div class="col-lg-3 col-md-6 col-12" v-for="(item, index) in mappedItems" v-bind:key="index">
        
            <card
              :title="item.name == 'unnamed' ? item.nameFix : item.name"
              :value="item.Q == null ? item.totalQuantity : item.Q"
              :image="item.lootIds"
              :iconBackground="stats.money.iconBackground"
              :detail="item.prop"
              directionReverse
            >         
      </card>
          
        </div>




          <div class="col-lg-3 col-md-6 col-12">
            <card
              :title="stats.users.title"
              :value="stats.users.value"
              :percentage="stats.users.percentage"
              :iconClass="stats.users.iconClass"
              :iconBackground="stats.users.iconBackground"
              :detail="stats.users.detail"
              directionReverse
            ></card>
          </div>
          <div class="col-lg-3 col-md-6 col-12">
            <card
              :title="stats.clients.title"
              :value="stats.clients.value"
              :percentage="stats.clients.percentage"
              :iconClass="stats.clients.iconClass"
              :iconBackground="stats.clients.iconBackground"
              :percentageColor="stats.clients.percentageColor"
              :detail="stats.clients.detail"
              directionReverse
            ></card>
          </div>
          <div class="col-lg-3 col-md-6 col-12">
            <card
              :title="stats.sales.title"
              :value="stats.sales.value"
              :percentage="stats.sales.percentage"
              :iconClass="stats.sales.iconClass"
              :iconBackground="stats.sales.iconBackground"
              :detail="stats.sales.detail"
              directionReverse
            ></card>
          </div>
        </div>
        <div class="row">
          <div class="col-lg-7 mb-lg">
            <!-- line chart -->
            <div class="card z-index-2">
              <gradient-line-chart />
            </div>
          </div>
          <div class="col-lg-5">
            <carousel />
          </div>
        </div>
        <div class="row mt-4">
          <div class="col-lg-7 mb-lg-0 mb-4">
            <div class="card">
              <div class="p-3 pb-0 card-header">
                <div class="d-flex justify-content-between">
                  <h6 class="mb-2">Sales by Country</h6>
                </div>
              </div>
              <div class="table-responsive">
                <table class="table align-items-center">
                  <tbody>
                    <tr v-for="(sale, index) in sales" :key="index">
                      <td class="w-30">
                        <div class="px-2 py-1 d-flex align-items-center">
                          <div>
                            <img :src="sale.flag" alt="Country flag" />
                          </div>
                          <div class="ms-4">
                            <p class="mb-0 text-xs font-weight-bold">Country:</p>
                            <h6 class="mb-0 text-sm">{{ sale.country }}</h6>
                          </div>
                        </div>
                      </td>
                      <td>
                        <div class="text-center">
                          <p class="mb-0 text-xs font-weight-bold">Sales:</p>
                          <h6 class="mb-0 text-sm">{{ sale.sales }}</h6>
                        </div>
                      </td>
                      <td>
                        <div class="text-center">
                          <p class="mb-0 text-xs font-weight-bold">Value:</p>
                          <h6 class="mb-0 text-sm">{{ sale.value }}</h6>
                        </div>
                      </td>
                      <td class="text-sm align-middle">
                        <div class="text-center col">
                          <p class="mb-0 text-xs font-weight-bold">Bounce:</p>
                          <h6 class="mb-0 text-sm">{{ sale.bounce }}</h6>
                        </div>
                      </td>
                    </tr>
                  </tbody>
                </table>
              </div>
            </div>
          </div>
          <div class="col-lg-5">
            <categories-card />
          </div>
        </div>
      </div>
    </div>
  </div>
</div>

</template>
<script>
import Card from "@/examples/Cards/Card.vue";
import GradientLineChart from "@/examples/Charts/GradientLineChart.vue";
import Carousel from "./components/Carousel.vue";
import CategoriesCard from "./components/CategoriesCard.vue";

import US from "@/assets/img/icons/flags/US.png";
import DE from "@/assets/img/icons/flags/DE.png";
import GB from "@/assets/img/icons/flags/GB.png";
import BR from "@/assets/img/icons/flags/BR.png";

export default {
  name: "dashboard-default",
  data() {
    return {
      stats: {
        money: {
          title: "Today's Money",
          value: "$53,000",
          percentage: "+55%",
          iconClass: "ni ni-money-coins",
          detail: "since yesterday",
          iconBackground: "bg-gradient-primary",
        },
        users: {
          title: "Today's Users",
          value: "2,300",
          percentage: "+3%",
          iconClass: "ni ni-world",
          iconBackground: "bg-gradient-danger",
          detail: "since last week",
        },
        clients: {
          title: "New Clients",
          value: "+3,462",
          percentage: "-2%",
          iconClass: "ni ni-paper-diploma",
          percentageColor: "text-danger",
          iconBackground: "bg-gradient-success",
          detail: "since last quarter",
        },
        sales: {
          title: "Sales",
          value: "$103,430",
          percentage: "+5%",
          iconClass: "ni ni-cart",
          iconBackground: "bg-gradient-warning",
          detail: "than last month",
        },
      },
      sales: {
        us: {
          country: "United States",
          sales: 2500,
          value: "$230,900",
          bounce: "29.9%",
          flag: US,
        },
        germany: {
          country: "Germany",
          sales: "3.900",
          value: "$440,000",
          bounce: "40.22%",
          flag: DE,
        },
        britain: {
          country: "Great Britain",
          sales: "1.400",
          value: "$190,700",
          bounce: "23.44%",
          flag: GB,
        },
        brasil: {
          country: "Brasil",
          sales: "562",
          value: "$143,960",
          bounce: "32.14%",
          flag: BR,
        },
      },

      server: 'int1',
      sid: 'eade0e533a2ea78abf76cf7e2368cfb4',
      inventoryData: '',
      jsonData:""
    };
  },
  components: {
    Card,
    GradientLineChart,
    Carousel,
    CategoriesCard,
  },

    methods: {
            async getHangaar() {
      const SID = 'eade0e533a2ea78abf76cf7e2368cfb4';
      const SERVER = 'int1';
      const INVENTORY_URL = `https://${SERVER}.darkorbit.com/flashAPI/inventory.php`;
      const headers = new Headers({
        "User-Agent": "BigPointClient/1.6.3",
        "dosid": SID,
        "Content-Type": "application/x-www-form-urlencoded"
      });


      const response = await fetch(INVENTORY_URL, {
        method: 'POST',
        headers: headers,
        credentials: "include",
        body: `action=getHangarList`
      });
      const result = await response.text();
      //this.jsonData = JSON.parse(atob(result));

      this.jsonData = JSON.parse(atob(result)).data.ret.hangars.find(i => i.hangar_is_active === true)
      console.log(this.jsonData.hangarID)
    },
    async getInventory() {
      this.getHangaar();
      console.log(this.jsonData)
      const SID = 'eade0e533a2ea78abf76cf7e2368cfb4';
      const SERVER = 'int1';
      const INVENTORY_URL = `https://${SERVER}.darkorbit.com/flashAPI/inventory.php`;
      const data = '{"params":{"hi":4051556}}';
      const encodedData = btoa(data);
      const headers = new Headers({
        "User-Agent": "BigPointClient/1.6.3",
        "dosid": SID,
        "Content-Type": "application/x-www-form-urlencoded"
      });


      const response = await fetch(INVENTORY_URL, {
        method: 'POST',
        headers: headers,
        credentials: "include",
        body: `action=getHangar&params=${encodedData}`
      });
      const result = await response.text();
      this.inventoryData = JSON.parse(atob(result));
    },

 
  },
    computed: {
       mappedItems() {
      try{

      let groupedItems = {}

      this.inventoryData.data.ret.items.forEach(item => {
        
        if (!groupedItems[item.L]) {
          if(item.properties != null){
          groupedItems[item.L] = {...item,totalQuantity: 1,prop: [item.properties]}
          }
          else{
          groupedItems[item.L] = {...item,totalQuantity: 1,prop:[]}
          }

        } else {
          if(item.properties != null){
          groupedItems[item.L].prop.push(item.properties)
          }
                    groupedItems[item.L].totalQuantity += 1


        }
      })

      return Object.values(groupedItems).map(item => {
        
        let itemCat = this.inventoryData.data.map.lootIds[item.L].replace("_", "/").replace("_", "/").replace("_", "/")
        let lootIds = "https://darkorbit-22.bpsecure.com/do_img/global/items/"+itemCat+"_30x30.png"
        let nameFixAux = itemCat.split("/")
        let nameFixOut = nameFixAux[nameFixAux.length - 1];
        let otherItem = this.inventoryData.data.ret.itemInfo.find(i => i.L === item.L)

        return {...item, ...otherItem, lootIds: lootIds, nameFix: nameFixOut }



      })

    }
    catch(error){return {error: "a"}}
    }
  }
};
</script>
