<template>


        <label>Server: <argon-input v-model="server" type="text" /></label>
        <br>
        <label>SID: <argon-input v-model="sid" type="text" /></label>
        <br>
        <button  class="btn btn-primary " @click="getInventory">Get Inventory</button>

<div v-if="mappedItems">

        <div class="row" v-for="(items, category) in mappedItems" :key="category">
         <div class="col-lg-4">
         <button class="btn btn-primary d-block w-40" type="button" data-bs-toggle="collapse" v-bind:data-bs-target="'#'+category" aria-expanded="false" v-bind:aria-controls="category">
    <div class="row d-flex">
             <div class="col-lg-4">

             <img           style="width:36px;"
 src="https://cdn-icons-png.flaticon.com/512/8923/8923721.png"/>
</div>
         <div class="col-lg-8">

    {{category}}
</div>
    </div>
  </button>
         </div> 

                  <div class="col-lg-4">  </div> 


         <div class="col-lg-4"></div> 


<div class="col-lg-3 col-md-6 col-12 collapse" v-for="item in items"  v-bind:key="item" v-bind:id="category">
        
              <card
                :title="item.name == 'unnamed' ? item.nameFix : item.name"
                :value="item.Q == null ? item.totalQuantity : item.Q"
                :image="item.lootIds"
                :iconBackground="''"
                :detail="item.property"
                directionReverse
              >         
              </card>
          
          </div>

        </div>
        </div>

 <!-- <div class="py-4 container-fluid">
      <div class="col-lg-12">
        <div class="row" v-for="(items, category) in mappedItems" :key="category">
        {{mappedItems}}
          <div class="col-lg-3 col-md-6 col-12" v-for="item in items"  v-bind:key="items.I">
        
              <card
                :title="item.name == 'unnamed' ? item.nameFix : item.name"
                :value="item.Q == null ? item.totalQuantity : item.Q"
                :image="item.lootIds"
                :iconBackground="stats.money.iconBackground"
                :detail="item.category"
                directionReverse
              >         
              </card>
          
          </div>
      </div>

      </div>
      </div>-->
      


</template>
<script>
import Card from "@/examples/Cards/Card.vue";

import ArgonInput from "@/components/ArgonInput.vue";

export default {
  name: "dashboard-default",
  data() {
    return {
      
      server: 'int1',
      sid: '',
      inventoryData: '',
      jsonData:""
    
  }},
  components: {
    Card,ArgonInput
  },

    methods: {
            async getHangaar() {
      const SID = this.sid;
      console.log(SID)
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
      console.log(this.jsonData)
    },
    async getInventory() {
      await this.getHangaar();
      console.log(this.jsonData)
      const SID = this.sid;
      const SERVER = 'int1';
      const INVENTORY_URL = `https://${SERVER}.darkorbit.com/flashAPI/inventory.php`;
      const data = '{"params":{"hi":'+this.jsonData.hangarID+'}}';
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

       var data_cleaned = Object.values(groupedItems).map(item => {
        
        let itemCat = this.inventoryData.data.map.lootIds[item.L].replace("_", "/").replace("_", "/").replace("_", "/")
        let lootIds = "https://darkorbit-22.bpsecure.com/do_img/global/items/"+itemCat+"_30x30.png"
        let nameFixAux = itemCat.split("/")
        let nameFixOut = nameFixAux[nameFixAux.length - 1];
        let category = nameFixAux;
        let otherItem = this.inventoryData.data.ret.itemInfo.find(i => i.L === item.L)

        return {...item, ...otherItem, lootIds: lootIds, nameFix: nameFixOut,category: category}



      })

          let categorized = {};
    data_cleaned.forEach(item => {
    let category = item.category[1];

    if (item.category.length === 2) {
      category = item.category[0];
    } else {
      category = item.category[1];
    }
    if(!categorized[category]) {
    categorized[category] = [];
    }
    categorized[category].push(item);
    });
  return categorized;

    }
    catch(error){return false}
    }
  }
};
</script>
