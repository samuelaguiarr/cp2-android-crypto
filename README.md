# cp2-android-crypto
Checkpoint 2 da disciplina Android Kotlin Developer

Aluno: Samuel Schaeffere Aguiar - 550212

Este projeto Android, desenvolvido em Kotlin, monitora a cotação de moedas criptográficas (no caso, Bitcoin) utilizando a API do Mercado Bitcoin. A aplicação faz uma requisição REST para obter o valor atual do Bitcoin em reais (BRL) e exibe o preço e a data/hora da última atualização. O usuário pode pressionar o botão “Atualizar” para buscar a cotação mais recente.

Evidência do funcionamento do projeto:

1ª cotação do Bitcoin:

![image](https://github.com/user-attachments/assets/5a00a879-d5cb-43e2-a032-bd803d48c610)

2ª cotação do Bitcoin:

![image](https://github.com/user-attachments/assets/c81b3170-00e6-4e07-954b-17c394986cbd)



Arquivos Kotlin do projeto:

TicketResponse.kt

![image](https://github.com/user-attachments/assets/3fb2989a-9cb2-4dcc-b3f0-ad9b7560a1cd)


MercadoBitcoinService.kt

![image](https://github.com/user-attachments/assets/c25ac3d5-c7cf-42a3-888a-794b13ecd9ac)



MercadoBitcoinServiceFactory.kt

![image](https://github.com/user-attachments/assets/f8a6a827-df45-45de-8a25-a0dd5116955d)


MainActivity.kt

![image](https://github.com/user-attachments/assets/69b8c502-acb5-4642-b032-752e0b13d223)

![image](https://github.com/user-attachments/assets/0486363e-a625-49b8-86df-7197857047f6)



Explicação do código

1. model/TickerResponse.kt
   
Eu criei essa classe TickerResponse como um “envelope” para o JSON que recebo da API. O corpo da resposta vem dentro de um campo ticker, então espelho isso aqui.

 ![image](https://github.com/user-attachments/assets/68239543-ee71-4bab-b8bc-ce54583a6ebc)
 
No objeto interno Ticker, eu mapeio cada campo que a API retorna:
high, low, vol, last, buy, sell viram String.
Date chega como timestamp Unix (segundos), então guardo num Long.

2. service/MercadoBitcoinService.kt
   
Importei o modelo TickerResponse, o tipo Response<T> do Retrofit e a anotação @GET.

![image](https://github.com/user-attachments/assets/43cae7be-19cb-4860-8957-117f745aa8d2)

Uso @GET("api/BTC/ticker/") para apontar o endpoint correto.
Marco o método como suspend, porque quero chamar dentro de um componente de programa que permite suspender e retomar a execução sem bloquear a UI.
Ele me devolve um Response<TickerResponse>, com tudo incluso (status, cabeçalhos e corpo).

3. factory/MercadoBitcoinServiceFactory.kt
   
Esse arquivo faz a “fábrica” do Retrofit, então importo o builder e o conversor Gson.

![image](https://github.com/user-attachments/assets/1c48a710-d4f5-4eb1-92a2-24557ab62051)

No create() eu configuro o baseUrl da API e adiciono GsonConverterFactory para interpretar o JSON.
Chamando retrofit.create(...) eu recebo uma instância pronta de MercadoBitcoinService.

4. main/MainActivity.kt
   
Importo tudo que vou usar: UI, coroutines, formatação e a minha factory de serviço.

![image](https://github.com/user-attachments/assets/717d378d-3deb-4bc5-8a85-7ba862b85117)

No onCreate eu uso o layout activity_main.xml.

![image](https://github.com/user-attachments/assets/131583c2-55a9-44ef-b098-32b84a31f40c)

Busco a Toolbar do XML e chamo meu método para customizar título e cores.

![image](https://github.com/user-attachments/assets/27c78d2a-b49c-4fab-bda6-d6c18ef6e698)

Aqui eu amarro o clique do botão “Atualizar” pra disparar minha requisição.

![image](https://github.com/user-attachments/assets/8baad870-972a-432b-89c7-1a4156735bf3)

Em configureToolbar eu registro a Toolbar como ActionBar e aplico cores/título.

![image](https://github.com/user-attachments/assets/ec553446-7db9-4d37-bdb1-2f578f6f87f2)

Eu inicio uma coroutine no Dispatcher.Main para rodar na UI.

![image](https://github.com/user-attachments/assets/2165a484-90c3-43c7-a061-b4309fba1801)

Instancio meu serviço e chamo getTicker(), aguardando a resposta.

![image](https://github.com/user-attachments/assets/68b6290d-3b22-4316-98dc-b5f3283be1a8)

Checo se o status HTTP está OK e pego o corpo sem série.

![image](https://github.com/user-attachments/assets/311a0847-5b61-4c43-87d7-8a584ce380ac)

Busco as TextView que vão mostrar valor e data na tela.

![image](https://github.com/user-attachments/assets/da2f1fe9-e699-4341-b352-82bf1bbd3b25)

Converto o last para Double, formatando como R$ usando Locale("pt","BR").

![image](https://github.com/user-attachments/assets/b0e4f043-3147-4455-8cb5-d93ef50eaa0e)

Transformo o timestamp em milissegundos, formatando a data/hora no padrão brasileiro.

![image](https://github.com/user-attachments/assets/9e048575-85db-4972-8a25-7197970e1008)

Se houver erro HTTP, trato alguns códigos comuns e mostro um Toast explicativo.

![image](https://github.com/user-attachments/assets/cbf3568b-ff0e-4595-975a-b60a9574516b)

Em caso de exceção (rede indisponível, JSON inesperado etc.), capturo tudo e exibo o erro na tela.



