<template>
  <v-container
    fluid
    style="
      width: 50%;
      display: flex;
      align-items: center;
      justify-content: flex-start;
      flex-direction: column;
      margin: 0 auto;
    "
  >
    <v-row>
      <v-col cols="6"><!-- item-text will change-->
        <v-combobox
          style="width: 100%"
          v-model="selectedSeller"
          @change="takeSellerUsername()"
          :items="storedPlan"
          label="Seller Username"
          item-text="sellerusername" 
          item-value="sellerusername"
          clearable small-chips outlined dense
        />
      </v-col>
       <v-col cols="6">
        <v-card
          class="mx-auto"
          max-width="800"
          tile
          v-for="item in sellerusername" :key="item.unique"
          >
          <v-list-item three-line>
              <v-list-item-content>
                  <v-list-item-title>{{returnInfo(item.unique).name}}</v-list-item-title>
                  <v-list-item-subtitle>Date {{item.date}}</v-list-item-subtitle>              
              </v-list-item-content>
          </v-list-item>
        </v-card>
      </v-col>
    </v-row>
  </v-container>
</template>

<script>
import axios from "axios";
import { mapState } from "vuex";
export default {
  name: "List",
  computed: {
    ...mapState(["username", "host","storedInfo"]),
  },
  data() {
    return {
      sellerusername:[],
      storedPlan:[],
      selectedSeller: null,
    };
  },
  methods: {
    takeSellerUsername() {
      this.sellerusername=[]
      for(let prop in this.storedPlan){
          if (this.selectedSeller.sellerusername == this.storedPlan[prop].sellerusername){
            this.sellerusername.push(this.storedPlan[prop].package)
        }
      }
    },
    returnInfo(unique){
        for(let prop in this.storedInfo){
          if (unique == this.storedInfo[prop].unique){
            return this.storedInfo[prop]
        }
      }
    },
    getStocks() {
      axios
        .put(this.host + "/list/userPlan", {
          username: this.username,
        }
      )
        .then((response) => {
            this.sellerUsername=response.data[0];
            this.storedPlan=response.data[0].plan
        }
      );
    },
    getInfo() {
      if(this.storedInfo.length==0){
        axios
          .get(this.host + "/list/planInfo")
          .then((response) => {
            this.$store.commit("setStoredInfo", response.data.data);
        });
      }
    },
  },
  created() {
      this.getStocks();
      this.getInfo();
  },
};
</script>
