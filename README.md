# Introdução 

O pipeline gráfico é um dos conceitos essenciais da Computação Gráfica, é importante entendê-lo para dominar as APIs gráficas OpenGL e DirectX, pois diversas funções destas API's agem sobre diferentes estágios do pipeline e a compreensão das etapas do pipelines farão o usuário ter uma melhor experiência ao utilizar estas API's.


# Objetivo

Através da implementação de um pipeline gráfico, iremos compreender melhor cada um dos processos que ocorrem até que os modelos geométricos cheguem até a tela. Ainda conheceremos com mais detalhes as dificuldades que ocorrem entre as transições e que estratégias podem ser utilizadas para superar tais dificuldades. Com estes conhecimentos, iremos ser capazes de conhecer com mais clareza as ferramentas gráficas que temos atualmente e acrescentar uma melhor experiência ao utilizá-las. 

# Estágios do Pipeline Gráfico

O pipeline gráfico consiste exatamente de uma sequência de passos necessários para transformar uma descrição geométrica/matemática de uma cena 3D em uma descrição visual na tela 2D. A imagem final é obtida por meio da rasterização das primitivas projetadas na tela. Basicamente, cada passo do pipeline gráfico consiste de uma transformação geométrica de um sistema de coordenadas (espaço) para outro, são eles :

((IMAGEM DOS ESTAGIOS DO PIPELINE))

## 1. Transformação: Espaço do Objeto → Espaço do Universo

Esta etapa do pipeline é responsável por transformar vértices, originalmente descritos no
espaço do objeto, para o espaço do universo. Isto é feito através da multiplicação destes
vértices por uma matriz denominada matriz de modelagem (ou model matrix). A matriz
de modelagem é composta por uma sequência de transformações geométricas que
posicionam o modelo no universo. As transformações que podem compor a matriz de
modelagem são a rotação, translação, escala e o cisalhamento (shear).

((space_object_to_world_space))

## Rotação

As rotações podem ser feitas nos três eixos de coordenadas, portanto seria necessário criar uma matriz para cada um dos tipos de rotação. Contudo, analisando a rotação em um dois eixos, é possível com algumas mudanças determinar as equações que irão rotacionar os vértices nos outros eixos.

Mas também podemos fazer isto para todos os eixos, misturando as rotações, rotacionando um objeto um pouco no eixo Y, depois em X e depois em Z.

Para calcular as novas posições dos vertices, também podemos escrever o valor de x de um vértice, pela distância dele até a origem multiplicada pelo cosseno do ângulo formado entre o vetor e o eixo X. O mesmo ocorre com o y de um vértice, que pode ser escrito pela distância multiplicada pelo seno do ângulo.

((rotacao))

Agora iremos rotacionar os vértices que desejamos:

((rotacao2))

Os calculos são os seguintes:

((rotacao3))

E por fim teremos os resultados:

((rotacao4))

No codigo faremos o seguinte para a rotação em X:

((rotacaox_codigo))

E obteremos:

((rotacaox))

Em Y segue os mesmos passos:

(rotacaoY_codigo)

E obteremos:

(rotacaoy)

Para rotacionar em ambos, primeiro rotacionamos no eixo X e dps no eixo Y assim:

(rotacaoXY_codigo)

E obteremos:

(rotacao_xy)

## Escala

Escalonar significa redimensionar um objeto, ou seja, aumentar ou diminuir suas dimensões, deformá-lo ou até mesmo espelhá-lo.  Para fazer isso, basta multiplicar os valores das coordenadas de um vértice por um fator de escala - lembrando que as coordenadas de um vértice possuem valores inteiros para X, Y e Z no modelo 3D e para X e Y no modelo 2D. Essa operação de multiplicação deve ser feita para todos os vértices do objeto. Ao longo da postagem, vamos adotar a seguinte notação : vetor transformado = matriz de transformação x vetor original. Cada vetor é uma generalização para os vértices do objeto. 

Matriz de escala:

(matriz_escala_codigo)

Aplicando a escala no eixo Y, aumentando 3 vezes:

(imagem escala y)


## Cisalhamento (Shear)

Uma outra forma de deformar os objetos de uma cena seria aplicando esta transformação. O shear  mantém uma coordenada U fixa enquanto muda a coordenada V ao longo de seu eixo, ou seja, há uma relação linear entre V e U.

(shear)

Matriz de Cisalhamento:

(shear_codigo)

Aplicando cisalhamento no eixo X:

(shear_x)

# 2. Transformação: Espaço do Universo → Espaço da Câmera

(espaco_camera)

Aqui é definido como a cena será vista, então, faz-se necessário configurar a câmera. A câmera possui 3 dados importantes que devem ser descritos:

- Posição da Câmera: Local onde ela se encontra
- Direction: Local para onde a câmera está "olhando"
- Up: Fixa a câmera no eixo determinado

Assim, é criado o código que representa a Criação da Câmera:

(codigo_camera)

Abaixo, é feito a parte que corresponde a essa etapa, o vertice, que agora possui os dados da etapa anterior, é multiplicado com a matriz view, criada para ser usada nessa etapa:

(codigo_matriz)

Inicialização da camera:

(codigo camera inicializacao)

# 3. Transformação: Espaço da Câmera → Espaço Projetivo ou de Recorte

Até agora já percorremos metade do pipeline gráfico. Esta terceira etapa consiste em transformar vértices do espaço da câmera para o espaço de recorte (ou projetivo) e mais uma vez iremos construir uma matriz, a matriz de projeção (projection matrix). Dada a seguinte situação :

(espaco_de_corte)

É interessante ressaltar que é nessa etapa onde ocorre a distorção perspectiva - objetos mais próximos do view plane aparentam ser maiores do que objetos que estão mais distantes.

Assim, é criado o código que representa a Matriz de Projeção:

Criação do View Plane - Matriz Projection:

(corte_codigo)

Com a matriz de projeção criada, multiplica-se agora com resultado da etapa anterior, da seguinte forma:

(projecao_vertice_codigo.png)

# 4. Transformação: Espaço de Recorte → Espaço “Canônico”

(recorte-canonico)

Esta etapa do pipeline é responsável por transformar pontos do espaço de recorte para o
espaço canônico (ou NDC – Normalized Device Coordinates). Isto é feito em duas
etapas. Primeiro, dividem-se as coordenadas dos vértices no espaço de recorte pela sua
coordenada homogênea. Esta transformação gera uma alteração da geometria da cena,
tornando menores os objetos que estiverem mais distantes da câmera. Adicionalmente,
esta transformação mapeia o volume de visualização (view frustum), com formato
piramidal, em um hexahedro. A seguir, os vértices são multiplicados por uma matriz
adicional que, ao aplicar escalas e translações, transforma estes vértices para o espaço
canônico, transformando o hexahedro anterior em um cubo de coordenadas unitárias e
centrado na origem.


Aqui ocorre a homogeneização, que no caso, significa dividir todos os componentes do vértice pela sua coordenada homogênea. Então, para fazer a divisão, fazemos o seguinte:

(canonico_codigo)

Como pode notar no código, é pego o último elemento, que é a coordenada homogênea, e assim, dividimos pelo resultado da etapa anterior, terminando essa etapa.

# 5. Transformação: Espaço “Canônico” → Espaço de Tela

(canonico)

Esta etapa do pipeline é responsável por transformar pontos do espaço canônico para o
espaço de tela. Isto é feito através da multiplicação dos vértices por uma matriz
específica que envolve escalas e translações.

Aqui é utilizado o viewport, onde aqui os vértices do espaço canônico é mapeado para o espaço da tela. Para isso ocorrer, deve-se lembrar que os valores são arredondados, pois a tela possui apenas coordenadas inteiras.

Então, eis o código que representa a Matriz Viewport:


Criação da Tela - Matriz Viewport


(viewportcodigo.png)

Com a viewport criada, ocorre a multiplicação da etapa anterior com a viewport:

(viewport_pipeline.png)

# Criação do Pipeline Gráfico


Esta parte consiste em mostrar como foi juntar os trechos anteriores em apenas uma função, comentando qual etapa corresponde a qual linha do código. Para isso, veja o código a seguir:

(pipeline_codigo.png)

# Resultados

Aqui poderemos comparar o resultado obtido com o resultado do loader disponibilizado pelo professor.

Loader do professor:
(imagem_macaco_gl)

Pipeline gerado:
(imagem_macaco_mygl)





