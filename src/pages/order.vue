<template>
    <div id="order" class="container">
        <h1>การสั่งซื้อของคุณ</h1>
        <show-ticket 
        v-for="order in orders" 
        :order="order"
        :id="order.id" 
        :enableCancel="true"
        :enablePrint="true"
        :key="order.id"
        @cancel="cancel(order.id)"
        >
        </show-ticket>
    </div>
</template>

<script>
import ShowTicket from '../components/booking/showTicket.vue'
import Helper from '../helper.js'
import swal from 'sweetalert';

export default {
    components:{
        ShowTicket
    },
    data: () => {
        return {
            account: null,
            orders: null
        }
    },
    created(){
        let orders = null

        this.account = Helper.getAccount()
        
        if(localStorage.getItem("purchests") != null){
            orders = JSON.parse(localStorage.getItem("purchests"))
            orders.forEach(order => {
                if(order.account.email == this.account.email){
                    this.orders = order.purchests
                }
            });
        }
    },
    methods:{
        cancel(orderId){
            swal({
                title: "ขอคืนเงินค่าบัตร",
                text: "หากคุณต้องการคืนเงิน ระบบจะโอนกลับเข้าสู่บัญชีท่านภายใน 1 ปี",
                icon: "warning",
                buttons: {
                    cancel:{
                        text: "ยกเลิก",
                        visible: true,
                    },
                    confirm:{
                        text: "คืนเงิน"
                    }
                }
            }).then(button =>{
                if(button){
                    let orders = JSON.parse(localStorage.getItem("purchests"))
                    let index = this.orders.map(x => x.id).indexOf(orderId)
                    this.orders.splice(index, 1)
        
                    orders.forEach(order => {
                        if(order.account.email == this.account.email){
                            order.purchests = this.orders
                        }
                    })
        
                    localStorage.setItem("purchests", JSON.stringify(orders))
                }
            })
        }
    }
}
</script>

<style lang="scss">
#order .booking-ticket .booking-ticket-item{
    padding: 20px 30px;
}

#order .booking-ticket .booking-ticket-item .total-price{
    margin: 0px;
}
</style>
