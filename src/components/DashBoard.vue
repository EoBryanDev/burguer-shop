<template>
    <div id="burguer-table" v-if="burgers">
        <AlertMessage :msg="msg" :idPedido="idPedido" v-show="msg"/>
        <div>
            <div id="burguer-table-heading">
                <div class="order-id">#</div>
                <div>Cliente</div>
                <div>Pão</div>
                <div>Carne</div>
                <div>Opcionais</div>
                <div>Ações</div>
            </div>
            <div id="burguer-table-rows">
                <div class="burguer-table-row" v-for="burger in burgers" :key="burger.id">
                    <div class="order-number"> {{burger.id}} </div>
                    <div>{{ burger.nome }}</div>
                    <div>{{ burger.pao }}</div>
                    <div>{{ burger.carne }}</div>
                    <div>
                        <ul>
                            <li v-for="(opcional,index) in burger.opcionais" :key="index">
                                {{opcional}}</li>
                        </ul>
                    </div>
                    <div>
                        <select name="status" class="status"
                         @change="updateBurguer($event, burger.id)">
                            <option value="">Selecione</option>
                            <option v-for="stats in status" :key="stats.id" 
                            :value="stats.tipo" :selected="burger.status == stats.tipo">{{stats.tipo}} </option>
                        </select>
                        <button class="delete-btn" @click="deletePedidos(burger.id)">Cancelar</button>
                    </div>
                </div>
                
            </div>
        </div>
    </div>
</template>

<script>
import AlertMessage from "./AlertMessage.vue";

    export default {
        name: "DashBoard",
        components: {
            AlertMessage
        },
        data(){
            return {
                burgers: null,
                burger_id: null,
                status: [],
                msg: null,
                idPedido: "",
            }

        },
        methods: {
            async getPedidos(){
                const req = await fetch("https://fake-api-burguer-shop.herokuapp.com/burgers");
                const data = await req.json()

                this.burgers = data;
                this.getStatus();

            },
            async getStatus(){
                const req = await fetch("https://fake-api-burguer-shop.herokuapp.com/status");
                const data = await req.json()
                this.status = data;
            },
            async deletePedidos(id){
                this.idPedido = id
                const req = await fetch(`https://fake-api-burguer-shop.herokuapp.com/burgers/${id}`,{
                    method: "DELETE"
                });
                this.msg = "DELETADO"              

                const res = await req.json();
                
                this.getPedidos();

                setTimeout(()=> {
                    this.msg = "";
                    this.idPedido = "";
               } , 2000)
            },
            async updateBurguer(e,id){
                
                const option = e.target.value;

                const dataJson = JSON.stringify({ status : option});
                const req = await fetch(`https://fake-api-burguer-shop.herokuapp.com/burgers/${id}`,{
                    method: "PATCH",
                    headers: {"Content-Type": "application/json"},
                    body: dataJson
                })
                const res = await req.json()
                this.idPedido = id
                this.msg = "ATUALIZADO" 
                setTimeout(()=> {
                    this.msg = "";
                    this.idPedido = "";
               } , 2000)

            }
        },
        mounted(){
            this.getPedidos();
            
        }
        
    }
    
</script>

<style scoped>
    #burguer-table{
        max-width: 1200px;
        margin: 0 auto;
    }

    #burguer-table-heading,
    #burguer-table-rows,
    .burguer-table-row {
        display: flex;
        flex-wrap: wrap;
    }
    #burguer-table-heading{
        font-weight: bold;
        padding: 12px;
        border-bottom: 3px solid #333;
    }

    #burguer-table-heading div,
    .burguer-table-row div{
        width: 19%;
    }

    .burguer-table-row{
        width: 100%;
        padding: 12px;
        border-bottom: 1px solid #ccc;
    }

    #burguer-table-heading .order-id,
    .burguer-table-row .order-number{
        width: 5%;
    }
    select{
        padding: 12px 6px;
        margin-right: 12px ;
    }

    .delete-btn{
        background-color: firebrick;
        color: #fff;
        font-weight: bold;
        padding: 10px;
        font-size: 16px;
        margin: 0 auto;
        cursor: pointer;

    }

    .delete-btn:hover{
        background: transparent;
        color: #fcba03;
    }
</style>