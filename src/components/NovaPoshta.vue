<template>
    <div class="NovaPoshta">
        <hr><br>
        <img src="https://upload.wikimedia.org/wikipedia/commons/thumb/9/97/Nova_Poshta_2014_logo.svg/2560px-Nova_Poshta_2014_logo.svg.png" alt="Nova Poshta - Future Delivery" width="300px"> <p>
        
        <!-- info -->
        <table>
            <p><strong>Список географических областей Украины (НП)</strong></p>
            <p>https://devcenter.novaposhta.ua/docs/services/556d7ccaa0fe4f08e8f7ce43/operations/556d9130a0fe4f08e8f7ce48</p>
            <p> Выбранный областной центр =>    {{selected.cityRef}}</p>
            <p><strong>Список отделений и их типов (НП)</strong></p>
            <p>https://devcenter.novaposhta.ua/docs/services/556d7ccaa0fe4f08e8f7ce43/operations/556d8211a0fe4f08e8f7ce45</p>
            <p> Выбранное отделение =>    {{selected.SettlementRef}}</p>
        </table>
        <br><hr><br>

        <!-- подгрузка областей -->
        <label>
            Выберите областной центр (AreasCenter):
            <select name="city" 
            v-model="selected.cityRef" 
            @click="loadSettlements">
                <option 
                v-for="city in cities" 
                :key="city.AreasCenter" 
                :value="city.AreasCenter">
                {{ city.Description }}
                </option>
            </select>
        </label>

        <!-- отделение по выбранной области -->
        <label v-if="selected.cityRef && settlements">
            Выберите отделение (SettlementRef):
            <select 
            name="city" 
            v-model="selected.SettlementRef">
                <option 
                v-for="(settlement, index) in settlements" 
                :key="index" 
                :value="settlement.SettlementRef">
                {{ settlement.Description }}
                </option>
            </select>
        </label>
    </div>
</template>

<script>
  export default {
    name: 'NovaPoshta',
    data() {
        return {
			cities: [],
            settlements: [],
            selected: {
                cityRef: '',
                settlementRef: '',
            }
		}
    },
    mounted() {
        fetch('https://api.novaposhta.ua/v2.0/json/', {
            method: 'POST',
            body: JSON.stringify({
                apiKey: "0555c556f433266e962e4ded47a8b3e9",
                modelName: "Address",
                calledMethod: "getAreas",
                methodProperties: {}
            })
        })
        .then((response) => response.json())
        .then((data) => this.cities = data.data)
    },
    methods: {
        loadSettlements() {
            fetch('https://api.novaposhta.ua/v2.0/json/', {
            method: 'POST',
            body: JSON.stringify({
                apiKey: "0555c556f433266e962e4ded47a8b3e9",
                modelName: "AddressGeneral",
                calledMethod: "getWarehouses",
                methodProperties: {
                    CityRef: this.selected.cityRef,
                    FindByString: "відділення"
                },
            })
        })
        .then((response) => response.json())
        .then((data) => this.settlements = data.data)
        }
    }
  }
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
    select {
        display: block;
        margin: 10px auto;
        padding: 6px;
        width: 500px;
        text-align: center;
    }

    table {
        width: 300px;
        margin: auto;
        border: 1px solid;
    }

    hr {
	margin: -30px auto 10px;
	padding: 0;
	height: 50px;
	border: none;
	border-bottom: 1px solid #1f1209;
	box-shadow: 0 20px 20px -20px #333;
	width: 95%;
}
</style>
