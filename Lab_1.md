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

## Exportar mapa para impressão
O GEE é excelente para processamento digital de imagens, mas não é o mais indicado para preparação de mapas para impressão. Para isso, vamos utilizar o QGIS.
Então, precisamos exportar a imagem classificada para, depois, importá-la no QGIS.

```JavaScript
//-----------------Preparar mapa temático para impressão--------------
// --- Exportar imagem classificada para o Google Drive---
Export.image.toDrive({image: classificada,//indica qual imagem será exportada 
                    description: 'Exportar_classificada', //descrição da tarefa que aparecerá em Tasks
                    folder: 'GB808',//pasta no Google Drive onde será salva a imagem.
                    fileNamePrefix: 'Classificada',//nome da imagem a ser salva                     
                    scale: 30,//resolução espacial que será salva a imagem 
                    });
```
A tarefa de exportação irá aparecer na aba 'Tasks'.
![image](https://user-images.githubusercontent.com/41900626/180433984-1c2e8922-20f6-4af7-844a-07c94f30fb1d.png)

Em 'Exportar_classificada', clique em _Run_.
Na janela que abre, clique em _Run_. Você pode modificar os parâmetros se julgar necessário.
![image](https://user-images.githubusercontent.com/41900626/180451150-ecbbcee8-8be8-41b1-a10b-6dd8af4c8b30.png)

Pronto, a imagem classificada está salva no seu Google Drive (![image](https://user-images.githubusercontent.com/41900626/180434989-c67e7765-7ddd-4e5d-8371-ea3a3e51ce0d.png)) e pode ser importada no QGIS.



## Preparando mapa para impressão no QGIS
Baixe a imagem 'Classificada.tif' para uma pasta no seu computador.
Abra o QGIS e importe a imagem.
Acesse as 'Propriedades' da imagem e observe o item 'Sistema de Referência de Coordenadas (SRC)'
![image](https://user-images.githubusercontent.com/41900626/180450171-288ace23-719a-4570-a818-78c9b54a67bb.png)
Como se pode ver, o SRC está como  'EPSG:32622 - WGS 84 / UTM zone 22N'. O que aparenta ser, em um primiero momento, um erro, é uma metodologia adotada pela distribuidora das cenas Landsat (USGS) para otimizar alguns processamentos. Veja mais informações na matéria ['Why do Landsat scenes in the Southern Hemisphere display negative UTM values?'](https://www.usgs.gov/faqs/why-do-landsat-scenes-southern-hemisphere-display-negative-utm-values).

Deixe o SRC como está.
Agora vamos identificar as classes com nomes que tragam um significado claro para o leitor do mapa.





-------
### Obrigado

