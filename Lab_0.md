# Sensoriamento Remoto
Lab 0 - Composição colorida e leitura de pixels
--------------

### Agradecimentos
- Google Earth Engine Team

------

### Prerequisitos
-------------
A conclusão deste exercício de laboratório requer o uso do navegador Google Chrome e uma conta do Google Earth Engine. Se você ainda não se inscreveu - faça-o agora em uma nova guia:

[Registro da conta do Earth Engine](https://signup.earthengine.google.com/)

Uma vez registrado, você pode acessar o ambiente do Earth Engine aqui: https://code.earthengine.google.com

A linguagem de programação JavaScript será utilizada nesse tutorial. Abordaremos o básico desta linguagem durante este curso. Se você quiser mais detalhes, pode ler a introdução fornecida aqui:

[JavaScript background](https://developers.google.com/earth-engine/tutorials/tutorials)

------------------------------------------------------------------------

### Objetivo
---------
O objetivo deste laboratório é fornecer uma introdução ao ambiente de processamento do GEE e realizar a leitura dos valores dos pixels de diferentes alvos da superfície terrestre.



## 1. O editor de código do GEE

![Fig. 1. O ambiente do GEE](https://github.com/geospatialeco/GEARS/blob/master/gee_editor.png)

1. Painel do Editor (_Code editor_)
	- O Painel do Editor é onde você escreve e edita seu código Javascript.
2. Painel Direito
	- Guia do console para saída de impressão.
	- Guia Inspetor para consultar os resultados do mapa.
	- Guia Tarefas para gerenciar tarefas de longa duração.
3. Painel Esquerdo
	- Aba Scripts: para gerenciar seus scripts de programação.
	- Aba Docs: para acessar a documentação dos objetos e métodos do Earth Engine, bem como alguns específicos do aplicativo Code Editor.
	- Aba Assets: Guia de dados geoespaciais que você criou e salvou, ou carregou.
4. Mapa Interativo
	- Para visualizar a saída das camadas do mapa
5. Barra de pesquisa
	- Para encontrar conjuntos de dados e locais de interesse.
6. Menu Ajuda
	- Documentação de referência do guia do usuário
	- Grupo de ajuda do fórum do Google para discutir o Earth Engine
	- Atalhos de teclado para o Editor de código
	- Visão geral do tour de recursos do Editor de código
	- Feedback para enviar feedback sobre o Code Editor
	- Sugerir um novo conjunto de dados.
---------

## 2. Procurando uma imagem

1. Logo acima do painel Codificação está a barra de pesquisa. 
Procure 'Piraquara', PR, nesta barra de pesquisa GEE e clique no resultado para deslocar e ampliar o mapa para Piraquara. Amplie/reduza o zoom usando a roda (scroll) do _mouse_.
![image](https://user-images.githubusercontent.com/41900626/178794965-07dde932-44de-4acf-a6d0-58649b7bad95.png)


2. Use a ferramenta de geometria 'Add a marker' para marcar um ponto sobre a cidade de Piraquara (uma vez selecionado o marcador é só clicar sobre o mapa base). Depois de criar o ponto de geometria, você o verá adicionado ao seu painel Codificação como uma variável (var) sob o título 'Imports'.
![image](https://user-images.githubusercontent.com/41900626/178795354-74c3042e-707d-4625-b806-5cb0f4b48141.png)

3. Renomeie o ponto resultante como 'paranagua' clicando no nome da 'Imports' (que é chamado de 'geometry' por padrão).
![image](https://user-images.githubusercontent.com/41900626/233175490-c880eab6-e85a-4b60-b3d4-82c8838e12f5.png)

 
 Nota: Você já pode salvar seu código em ![image](https://user-images.githubusercontent.com/41900626/178795780-e672f2b2-2472-4e9c-b32c-8caef6e928da.png)
. Salvei com o nome 'Lab0'.

4. Procure por 'Landsat 9 Level 2' na barra de pesquisa. Na seção de resultados, você verá 'USGS Landsat 9 Level 2, Collection 2, Tier 1'.



Clique nele e observe as importantes descrições do tipo de produto, como resolução espacial e radiométrica. Navegue pelas abas 'Description', 'Bands' e 'Image Properties'. Uma melhor visualização é alcançada clicando no canto superior direito, conforme indicado na figura.
![image](https://user-images.githubusercontent.com/41900626/233184861-990d8678-d221-4dd5-8e78-99481f4bfeab.png)



Após essa analise, volte alguns passos e clique no botão 'Import'.
![image](https://user-images.githubusercontent.com/41900626/233185823-21fff982-9614-4789-be7f-829f87ed7f50.png)


5. Após clicar em 'Import', as imagens/bandas Landsat-9 serão adicionadas às nossas importações ('Imports') no painel de Codificação como uma variável (var). Ele será listado abaixo do ponto de geometria do cidade de 'paranagua' com o nome padrão "imageCollection" (coleção de imagens). Vamos renomeá-lo para “land9” clicando em 'imageCollection' e digitando “land9”.
![image](https://user-images.githubusercontent.com/41900626/233172525-7c150f6c-dafb-4b5b-99b7-274703f80863.png)


6. É importante entender que agora adicionamos acesso à coleção completa de imagens do Landsat-9 ao nosso script (ou seja, todas as imagens que foram coletadas até a data de hoje). Para este exercício, não queremos carregar todas essas imagens - queremos uma única imagem, livre de nuvens, sobre a cidade de Piraquara. Dessa forma, devemos filtrar a coleção de imagens utilizando alguns critérios, como intervalo de aquisição, localização espacial e cobertura de nuvens.




### Filtrando coleções de imagens

7. Para filtrar a coleção, precisamos usar um pouco de codificação. Na linguagem de programação JavaScript, duas barras (//) indicam linhas de comentários e são ignoradas quando rodamos o processamento. Usamos // para escrever notas para nós mesmos em nosso código, para que nós (e outros que queiram usar nosso código) possamos entender por que estamos utilizando o código escolhido.
Você pode digitar manualmente o código abaixo, o que é legal para aprender a linguagem, ou simplesmente copiar todo o código e colá-lo na caixa do editor de código do GEE.

```JavaScript
var imagem = ee.Image(land9 //Coleção de imagens 
            .filterDate("2021-01-01", "2023-04-19") //filtro de datas
            .filterBounds(paranagua) //filtro de local
            .sort("CLOUD_COVER") //organizar imagens pela % de cobertura de nuvens 
            .first()); //seleciona a primeira imagem desta coleção - ou seja, a imagem com menor cobertura de nuvens
  
print("Uma cena do Landsat 9:", imagem); // imprime a imagem selecionada no console.
```

8. Em seguida, clique no botão "Run" (executar) e veja o GEE fazer sua mágica... 
Este pedaço de código pesquisará o arquivo completo do Landsat-9, encontrará imagens localizadas em Piraquara, PR (na interseção com o ponto que você escolheu, para ser mais preciso), irá classificá-las de acordo com a porcentagem de cobertura de nuvens e, em seguida, retornará a imagem com menor cobertura de nuvens. As informações relacionadas a esta imagem serão impressas no Console (lado direito), onde está listada como "Uma cena do Landsat 9:" com alguns detalhes sobre essa cena.
Consegues descobrir quando foi coletada a cena?.
![image](https://user-images.githubusercontent.com/41900626/233186377-ec8688f4-6041-4303-8b02-cb21f9de91b1.png)





## Adicionando imagens à visualização do mapa ('Map view')
1. Agora, para realmente dar uma olhada nesta imagem, precisamos adicioná-la ao nosso ambiente de mapeamento (similarmente a um SIG como QGIS). Antes de fazer isso, no entanto, vamos definir como queremos exibir a imagem. Vamos começar com uma representação de cores verdadeiras, digitando as linhas abaixo (clique "Run" sempre que quiser executar o código).

```JavaScript
  // Adiciona uma composição RGB em cores verdadeiras (Bandas 3,2 e 1) ao mapa, primeiramente sem contraste.
   Map.addLayer(imagem, {bands: ["SR_B4", "SR_B3", "SR_B2"]}, "sem contraste");
```
![image](https://user-images.githubusercontent.com/41900626/233187338-d682c43c-1e35-426e-91d1-25abc4b62a43.png)

Observe como a composição colorida se apresenta bastante escura.


Agora, vamos definir um contraste para melhorar a vizualização da nossa composição colorida. 
```JavaScript
  // Defina os parâmetros de visualização em um dicionário JavaScript para renderização de cores verdadeiras. Bandas 3,2 e 1 são necessárias para tal.
    var parVizualizacao = {
        bands: ["SR_B4", "SR_B3", "SR_B2"],//A serem associadas ao canais de cores R-G-B
        min: 7000,//Os valores em min: e max: definem os limiares de contraste a ser aplicado
        max: 12000
        };

  // Adicione a imagem ao mapa, usando os parâmetros de visualização.
  Map.addLayer(imagem, parVizualizacao, "imagem de cor verdadeira");
```
![image](https://user-images.githubusercontent.com/41900626/233188509-d466dcb2-4322-4c70-a9fa-3192b982280f.png)



10. Este código especifica que para uma imagem de cores verdadeiras, as bandas 4,3 e 2 devem ser usadas na composição RGB. Depois que a imagem aparecer no mapa, você poderá ampliar e explorar Piraquara e arredores. Os símbolos (+) e (-) no canto superior esquerdo do ambinete de mapa podem ser usados para aplicar diferentes níveis de zoom na cena (também possível com a roda de rolagem (scroll) do mouse/trackpad). 
Um clique com o botão esquerdo do mouse abre a "mão" para mover a imagem ao redor. Mover o mouse sobre o botão 'Layers' (camadas) no canto superior direito do painel do mapa mostra as camadas disponíveis e permite ajustar a opacidade das diferentes camadas.
Experimente diferentes limiares de contraste alterando os valores de 'min:' (valor mínimo) e 'max:' (valor máximo).



## Crie composições coloridas com diferentes bandas espectrais

Você aprendeu acima como criar uma composição colorida em cores verdadeiras. 
Agora crie composições falsa-cor e coloque as bandas nos filtros de cor seguindo as seguintes sequências (Nota: provavelmente você terá que ajustar também o valor de 'min:' e 'max:' para definir um contraste ótimo para a nova composição):

a) R4-G5-B6 (ex. bands: ["SR_B4", "SR_B5", "SR_B6"])

b) R5-G6-B4

c) R6-G4-B5

d) R6-G5-B4



## Lendo os valores do pixels
Agora, vamos fazer a leitura de pixels para os seguintes alvos/temas: vegetação, água, área urbana e solo. 
Vamos começar com vegetação. Para isso aproxime até algum tipo de vegetação.
Dica: Comente a camada com a imagem sem contraste, comentando a linha de código (adicione '//' na frente) para evitar de carregar uma camada que não usaremos:
```JavaScript
   //Map.addLayer(imagem, {bands: ["SR_B4", "SR_B3", "SR_B2"]}, "sem contraste");
```

Agora clique na aba 'Inspector' no lado direito, parte superior. Como a própira janela sugere '_Click on the map to inspect the layers._' ao clicar no mapa, você pode inspecionar o valor dos pixels em cada banda espectral. Ao clicar no mapa, os valores das Bandas no pixel clicado aparecerão na janela 'Inspector'.

![image](https://user-images.githubusercontent.com/41900626/233383135-bdcf384d-a47d-4b8c-8af8-35823a5b09a7.png)

Talvez os valores dos pixels são mostrados em forma de gráfico de barras verticais.
Ao clicar em 'List view' (![image](https://user-images.githubusercontent.com/41900626/179016714-127467d3-a359-41e5-ad59-12e13c668a63.png)), os valores exatos são mostrados na forma de uma lista.

![image](https://user-images.githubusercontent.com/41900626/233383631-576ff837-ee80-4d70-9c0f-f6283a2d0881.png)


Alternativamente, podemos mostrar em um gráfico plano-cartesiano os valores dos pixels em cada banda:

```Javascript
//Plotar valor das bandas em gráfico  
var chart = ui.Chart.image.regions({
  image:imagem.select([ "SR_B2",  "SR_B3", "SR_B4", "SR_B5", "SR_B6", "SR_B7",]), 
  regions:paranagua, 
});
print(chart)
```
![image](https://user-images.githubusercontent.com/41900626/233391580-45e6da89-0212-47e3-b8ca-b85145d29d48.png)
Nessa opção, contudo, devemos criar para cada classe um ponto. No exemplo acima, o gráfico é produzido a partir do ponto representando 'paranagua'.



Repita a leitura de pixels para os outros alvos e observe as diferenças de valores entre eles e como isso se relaciona com o conteúdo teórico.
Dica: Para uma boa interpretação é sempre bom olhar o comprimento de onda a que cada banda se refere, o que pode ser feito consultando os detalhes da imagem, como por exemplo:
![image](https://user-images.githubusercontent.com/41900626/233384462-9c115891-d8cd-46d1-968a-bd1e14c3a372.png)



## Obrigado
Espero que esse tutorial tenha sido útil.

Atenciosamente, Elias F Berra

