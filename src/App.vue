<script setup>
 import { ref, reactive } from 'vue';
 import axios from 'axios'; // Importando Axios
 
 // Dados do pedido (orderData)
 const orderData = reactive({
   title: 'produto nome',
   quantity: 1,
   price: 100,
 });
 
 // MercadoPago initialization
 const mp = new MercadoPago('APP_USR-b2ad37f2-01f8-4ed9-b5be-7ddb974c6eb0', { locale: 'pt-BR' });
 
 // Reactive state to hold preference ID
 const preferenceId = ref(null);
 
 // Função para criar o botão de checkout após obter o ID da preferência
 const createCheckoutButton = (preferenceId) => {
   const bricksBuilder = mp.bricks();
 
   const renderComponent = async () => {
     // Remover qualquer botão anterior, se existir (gerenciado pelo Vue agora)
     // Criar o botão de checkout do Mercado Pago no 'wallet_container'
     await bricksBuilder.create('wallet', 'wallet_container', {
       initialization: {
         preferenceId: preferenceId,
       },
     });
   };
 
   renderComponent();
 };
 
 // Função para manipular o clique e buscar os dados de preferência
 const handleCheckoutClick = async () => {
   try {
     // Enviar uma requisição para o backend para criar a preferência
     const response = await axios.post('https://backend-api-mercadopago.onrender.com/create_preference', orderData, {
       headers: {
         'Content-Type': 'application/json',
       },
     });
 
     // Obter o ID da preferência da resposta
     const preference = response.data;
 
     // Armazenar o ID da preferência e criar o botão de checkout
     preferenceId.value = preference.id;
     createCheckoutButton(preference.id);
   } catch (error) {
     // Tratar erros da requisição
     alert('Erro: Não foi possível criar a preferência de pagamento.');
     console.error(error);
   }
 };
 </script>
 
 <template>
   <div>Preferência ID: {{ preferenceId }}</div>
 
   <!-- Botão para iniciar o pagamento -->
   <button @click="handleCheckoutClick">Clique para pagar</button>
 
   <!-- Container para o botão do Mercado Pago -->
   <div id="wallet_container"></div>
 </template>