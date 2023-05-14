# Sensoriamento Remoto
Lab 1 - Composição colorida 
--------------


### Prerequisitos
-------------
A conclusão deste exercício de laboratório requer o uso do navegador Google Chrome e uma conta do Google Earth Engine. Se você ainda não se inscreveu - faça-o agora em uma nova guia:

[Registro da conta do Earth Engine](https://signup.earthengine.google.com/)

Uma vez registrado, você pode acessar o ambiente do Earth Engine aqui: https://code.earthengine.google.com

O Google Earth Engine usa a linguagem de programação JavaScript. Abordaremos o básico desta linguagem durante este curso. Se você quiser mais detalhes, pode ler a introdução fornecida aqui:

[JavaScript background](https://developers.google.com/earth-engine/tutorials/tutorials)

------------------------------------------------------------------------
### Objetivo

O objetivo deste laboratório é criar uma composição colorida falsa-cor de Mato Rico, PR, e exportar essa composição para o QGIS para impressão final.

----------

## Importando limite territorial de Mato Rico, PR
Vamos utilizar os limites político-administrativos municipais conforme compilado pela FAO ('FAO GAUL: Global Administrative Unit Layers 2015, Second-Level Administrative Units').
Nota: Para trabalhos que exigem maior precisão cartográfica no território brasileiro, recomenda-se a utilização das malhas municipais do IBGE.

```JavaScript
//------------Importar limite de Mato Rico, PR, com dados da FAO 

var zeroLevel = ee.FeatureCollection("FAO/GAUL/2015/level0");// País                      
var firstLevel = ee.FeatureCollection("FAO/GAUL/2015/level1");//Estado
var secondLevel = ee.FeatureCollection("FAO/GAUL/2015/level2") ;//Município


var Mato_Rico = secondLevel.filter(ee.Filter.eq('ADM2_NAME', 'Mato Rico'))
Map.addLayer(Mato_Rico, {}, 'Mato Rico')
```

Você deve estar visualizando o município de Mato Rico, PR.
![image](https://github.com/eliasberra/Sensoriamento_remoto_GB808/assets/41900626/871d518c-0602-494a-a067-9405cb797bb2)



## Selecionar imagem do satélite Landsat 9 e recortar para Mato Rico

```JavaScript
//------------Selecionar imagem do satélite Landsat 9 e recortar para Mato Rico
var imagem_selecionada = 
      ee.ImageCollection('LANDSAT/LC09/C02/T1_L2') 
    .filterBounds(Mato_Rico)
    .filterDate('2023-05-01', '2023-05-30')  
    .median()//mediana de todos os valores em cada pixel
    .clip(Mato_Rico)//recorta para mato Rico
```

Agora vamos criar duas composições coloridas: 

1) composição cores-verdadeiras:
```JavaScript
//composição cor-verdadeira
var corVerdadeira = {bands:['SR_B4',//Banda do vermelho
                    'SR_B3',//Banda do verde
                    'SR_B2'],//Banda do azul
                     min:7000, max: 20000};//Contraste da composição   
```
               

2) composição falsa-cor:
```JavaScript
//composição falsa-cor
var falsaCor = {bands:['SR_B6',//Infravermelho médio
                    'SR_B5',//Infravermelho próximo 
                    'SR_B4'],//Vermelho
                    min:7000, max: 25000};//Contraste da composição   
```

Adicione no visualizador de mapas as duas composições
```JavaScript
Map.addLayer(imagem_selecionada, corVerdadeira, 'Mato Rico-cor verdadeira');
Map.addLayer(imagem_selecionada, falsaCor, 'Mato Rico-falsa cor');
```

Você deve estar visualizando as duas composições, as quais estão disponíveis na aba 'Layers'.
![image](https://github.com/eliasberra/SR_GB808_2023/assets/41900626/13ffeb8e-0da8-410e-8559-ab43c64e6e13)

Escolha uma das duas composições para exportar para impressão. Você pode basear sua escolha respondendo: Em qual das composições as feições terrestres ficam melhor fotoidentificáveis?

## Exportar mapa para impressão
O GEE é excelente para processamento digital de imagens (PDI), mas não é o mais indicado para preparação/edição de mapas para impressão. Para isso, vamos utilizar o software QGIS.
Então, precisamos exportar a imagem selecionada para, depois, importá-la no QGIS.
Primeiramente, vamos transformar a composição escolhida em uma imagem RGB para exportação.
```JavaScript
//------------Preparar uma composição colorida RGB para exportação
 var composicaoEscolhida = imagem_selecionada.visualize(falsaCor);// OU corVerdadeira
```

Agora vamos exportar essa composição RGB para o Google Drive.

```JavaScript
//------------Preparar uma composição colorida RGB para exportação
 var composicaoEscolhida = imagem_selecionada.visualize(falsaCor);// OU corVerdadeira
 
//------------ Exportar imagem RGB para o Google Drive---
Export.image.toDrive({image: composicaoEscolhida,//indica qual imagem será exportada 
                    description: 'Exportar_RGB', //descrição da tarefa que aparecerá em Tasks
                    folder: 'GB808',//pasta no Google Drive onde será salva a imagem.
                    fileNamePrefix: 'Composicao_Colorida_654',//nome da imagem a ser salva                     
                    region: Mato_Rico,//Tamanho da imagem a ser exportada
                    scale: 30,//resolução espacial na qual será salva a imagem                     
                    });
```
A tarefa de exportação irá aparecer na aba 'Tasks' (canto direito superior).


Em 'Exportar_RGB', clique em _Run_.
![image](https://github.com/eliasberra/SR_GB808_2023/assets/41900626/a3609200-0fc0-43fa-8ac6-8287a8831949)
Na janela que abre, clique novamente em _Run_. Você pode modificar os parâmetros se julgar necessário.
![image](https://github.com/eliasberra/SR_GB808_2023/assets/41900626/680f20e5-c065-4af4-93be-4cc9bc42b3ee)




Pronto, a imagem RGB está salva no seu Google Drive, de acordo com o nome definido acima em 'folder:' (![image](https://user-images.githubusercontent.com/41900626/180434989-c67e7765-7ddd-4e5d-8371-ea3a3e51ce0d.png)). Agora, ela pode ser baixada e importada no QGIS.



## Preparando mapa para impressão no QGIS
Baixe a imagem 'Composicao_Colorida_654.tif' para uma pasta no seu computador.
Abra o QGIS e importe a imagem. Você deve estar visualizando algo parecido com a captura de tela abaixo.
![image](https://github.com/eliasberra/SR_GB808_2023/assets/41900626/b95ab977-4aab-4e22-a61b-238502500e9d)

Dica: Você pode atibuir transparência à cor preta (pixels com valores = 0)
![image](https://github.com/eliasberra/SR_GB808_2023/assets/41900626/f93926c7-ada0-4608-9481-be7765d9669d)


Agora, no QGIS, acesse o Layout de Impressão e prepare um mapa para impressão (bem bonito!). Lembre das aulas de Cartografia Temática, onde utilizamos banstante essa funcionalidade.
![image](https://github.com/eliasberra/SR_GB808_2023/assets/41900626/9814fe8e-4a71-406f-92cc-f114aae839bd)








-------
### Obrigado


