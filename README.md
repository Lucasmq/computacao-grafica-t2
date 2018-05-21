# Introdução 

O pipeline gráfico é um dos conceitos essenciais da Computação Gráfica, é importante entendê-lo para dominar as APIs gráficas OpenGL e DirectX, pois diversas funções destas API's agem sobre diferentes estágios do pipeline e a compreensão das etapas do pipelines farão o usuário ter uma melhor experiência ao utilizar estas API's.


# Objetivo

Através da implementação de um pipeline gráfico, iremos compreender melhor cada um dos processos que ocorrem até que os modelos geométricos cheguem até a tela. Ainda conheceremos com mais detalhes as dificuldades que ocorrem entre as transições e que estratégias podem ser utilizadas para superar tais dificuldades. Com estes conhecimentos, iremos ser capazes de conhecer com mais clareza as ferramentas gráficas que temos atualmente e acrescentar uma melhor experiência ao utilizá-las. 

# Estágios do Pipeline Gráfico

O pipeline gráfico consiste exatamente de uma sequência de passos necessários para transformar uma descrição geométrica/matemática de uma cena 3D em uma descrição visual na tela 2D. A imagem final é obtida por meio da rasterização das primitivas projetadas na tela. Basicamente, cada passo do pipeline gráfico consiste de uma transformação geométrica de um sistema de coordenadas (espaço) para outro, são eles :

![alt text](https://github.com/Lucasmq/computacao-grafica-t2/blob/master/imagens/pipeline.png)

Para a parte de rasterização será utilizado a implementação referente ao trabalho 1 com algumas alterações para o uso no trabalho 2.

## 1. Transformação: Espaço do Objeto → Espaço do Universo

Esta etapa do pipeline é responsável por transformar vértices, originalmente descritos no
espaço do objeto, para o espaço do universo. Isto é feito através da multiplicação destes
vértices por uma matriz denominada matriz de modelagem (ou model matrix). A matriz
de modelagem é composta por uma sequência de transformações geométricas que
posicionam o modelo no universo. As transformações que podem compor a matriz de
modelagem são a rotação, translação, escala e o cisalhamento (shear).

![alt text](https://github.com/Lucasmq/computacao-grafica-t2/blob/master/imagens/space_object_to_world_space.png)


## Rotação

As rotações podem ser feitas nos três eixos de coordenadas, portanto seria necessário criar uma matriz para cada um dos tipos de rotação. Contudo, analisando a rotação em um dois eixos, é possível com algumas mudanças determinar as equações que irão rotacionar os vértices nos outros eixos.

Mas também podemos fazer isto para todos os eixos, misturando as rotações, rotacionando um objeto um pouco no eixo Y, depois em X e depois em Z.

Para calcular as novas posições dos vertices, também podemos escrever o valor de x de um vértice, pela distância dele até a origem multiplicada pelo cosseno do ângulo formado entre o vetor e o eixo X. O mesmo ocorre com o y de um vértice, que pode ser escrito pela distância multiplicada pelo seno do ângulo.

![alt text]( https://github.com/Lucasmq/computacao-grafica-t2/blob/master/imagens/rotacao.png )

Agora iremos rotacionar os vértices que desejamos:

![alt text]( https://github.com/Lucasmq/computacao-grafica-t2/blob/master/imagens/rotacao2.png )

Os calculos são os seguintes:

![alt text]( https://github.com/Lucasmq/computacao-grafica-t2/blob/master/imagens/rotacao3.png )


E por fim teremos os resultados:

![alt text]( https://github.com/Lucasmq/computacao-grafica-t2/blob/master/imagens/rotacao4.png )


No codigo faremos o seguinte para a rotação em X:


![alt text]( https://github.com/Lucasmq/computacao-grafica-t2/blob/master/imagens/rotacao_x_codigo.png )

E obteremos:


![alt text]( https://github.com/Lucasmq/computacao-grafica-t2/blob/master/imagens/rotacaox.png)


Em Y segue os mesmos passos:


![alt text]( https://github.com/Lucasmq/computacao-grafica-t2/blob/master/imagens/rotacao_y_codigo.png)


E obteremos:


![alt text]( https://github.com/Lucasmq/computacao-grafica-t2/blob/master/imagens/rotacaoy.png)

Para rotacionar em ambos, primeiro rotacionamos no eixo X e dps no eixo Y assim:


![alt text](https://github.com/Lucasmq/computacao-grafica-t2/blob/master/imagens/rotacaoxy_codigo.png )

E obteremos:


![alt text]( https://github.com/Lucasmq/computacao-grafica-t2/blob/master/imagens/rotacaoXY.png)

## Escala

Escalonar significa redimensionar um objeto, ou seja, aumentar ou diminuir suas dimensões, deformá-lo ou até mesmo espelhá-lo.  Para fazer isso, basta multiplicar os valores das coordenadas de um vértice por um fator de escala - lembrando que as coordenadas de um vértice possuem valores inteiros para X, Y e Z no modelo 3D e para X e Y no modelo 2D. Essa operação de multiplicação deve ser feita para todos os vértices do objeto. Ao longo da postagem, vamos adotar a seguinte notação : vetor transformado = matriz de transformação x vetor original. Cada vetor é uma generalização para os vértices do objeto. 

Matriz de escala:


![alt text]( https://github.com/Lucasmq/computacao-grafica-t2/blob/master/imagens/matriz_escala_codigo.png)

Aplicando a escala no eixo Y, aumentando 3 vezes:


![alt text](https://github.com/Lucasmq/computacao-grafica-t2/blob/master/imagens/scala_y.png )

## Cisalhamento (Shear)

Uma outra forma de deformar os objetos de uma cena seria aplicando esta transformação. O shear  mantém uma coordenada U fixa enquanto muda a coordenada V ao longo de seu eixo, ou seja, há uma relação linear entre V e U.


![alt text](https://github.com/Lucasmq/computacao-grafica-t2/blob/master/imagens/shear.png )


Matriz de Cisalhamento:


![alt text](https://github.com/Lucasmq/computacao-grafica-t2/blob/master/imagens/shear_matriz.png )


Aplicando cisalhamento no eixo X:


![alt text]( https://github.com/Lucasmq/computacao-grafica-t2/blob/master/imagens/shear_x.png)

# 2. Transformação: Espaço do Universo → Espaço da Câmera


![alt text]( https://github.com/Lucasmq/computacao-grafica-t2/blob/master/imagens/espaco_camera.png)


Aqui é definido como a cena será vista, então, faz-se necessário configurar a câmera. A câmera possui 3 dados importantes que devem ser descritos:

- Posição da Câmera: Local onde ela se encontra
- Direction: Local para onde a câmera está "olhando"
- Up: Fixa a câmera no eixo determinado

Assim, é criado o código que representa a Criação da Câmera:


![alt text](https://github.com/Lucasmq/computacao-grafica-t2/blob/master/imagens/codigo_camera.png )


Abaixo, é feito a parte que corresponde a essa etapa, o vertice, que agora possui os dados da etapa anterior, é multiplicado com a matriz view, criada para ser usada nessa etapa:


![alt text]( https://github.com/Lucasmq/computacao-grafica-t2/blob/master/imagens/codigo_mult%20matrix.png)


Inicialização da camera:


![alt text](https://github.com/Lucasmq/computacao-grafica-t2/blob/master/imagens/criacao_da_camera_codigo.png )


# 3. Transformação: Espaço da Câmera → Espaço Projetivo ou de Recorte

Até agora já percorremos metade do pipeline gráfico. Esta terceira etapa consiste em transformar vértices do espaço da câmera para o espaço de recorte (ou projetivo) e mais uma vez iremos construir uma matriz, a matriz de projeção (projection matrix). Dada a seguinte situação :


![alt text]( https://github.com/Lucasmq/computacao-grafica-t2/blob/master/imagens/espaco_de_corte.png)


É interessante ressaltar que é nessa etapa onde ocorre a distorção perspectiva - objetos mais próximos do view plane aparentam ser maiores do que objetos que estão mais distantes.

Assim, é criado o código que representa a Matriz de Projeção:

Criação do View Plane - Matriz Projection:


![alt text](https://github.com/Lucasmq/computacao-grafica-t2/blob/master/imagens/corte_codigo.png )


Com a matriz de projeção criada, multiplica-se agora com resultado da etapa anterior, da seguinte forma:


![alt text](https://github.com/Lucasmq/computacao-grafica-t2/blob/master/imagens/projecao_vertice_codigo.png )


# 4. Transformação: Espaço de Recorte → Espaço “Canônico”

![alt text]( https://github.com/Lucasmq/computacao-grafica-t2/blob/master/imagens/recorte-canonico.png)


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


![alt text](https://github.com/Lucasmq/computacao-grafica-t2/blob/master/imagens/canonico_codigo.png )


Como pode notar no código, é pego o último elemento, que é a coordenada homogênea, e assim, dividimos pelo resultado da etapa anterior, terminando essa etapa.

# 5. Transformação: Espaço “Canônico” → Espaço de Tela


![alt text]( https://github.com/Lucasmq/computacao-grafica-t2/blob/master/imagens/canonico.png)


Esta etapa do pipeline é responsável por transformar pontos do espaço canônico para o
espaço de tela. Isto é feito através da multiplicação dos vértices por uma matriz
específica que envolve escalas e translações.

Aqui é utilizado o viewport, onde aqui os vértices do espaço canônico é mapeado para o espaço da tela. Para isso ocorrer, deve-se lembrar que os valores são arredondados, pois a tela possui apenas coordenadas inteiras.

Então, eis o código que representa a Matriz Viewport:


Criação da Tela - Matriz Viewport



![alt text]( https://github.com/Lucasmq/computacao-grafica-t2/blob/master/imagens/viewportcodigo.png)


Com a viewport criada, ocorre a multiplicação da etapa anterior com a viewport:


![alt text]( https://github.com/Lucasmq/computacao-grafica-t2/blob/master/imagens/viewport_pipeline.png)


# Criação do Pipeline Gráfico


Esta parte consiste em mostrar como foi juntar os trechos anteriores em apenas uma função, comentando qual etapa corresponde a qual linha do código. Para isso, veja o código a seguir:


![alt text]( https://github.com/Lucasmq/computacao-grafica-t2/blob/master/imagens/pipeline_codigo.png)


# Resultados

Aqui poderemos comparar o resultado obtido com o resultado do loader disponibilizado pelo professor.

Loader do professor:

![alt text]( https://github.com/Lucasmq/computacao-grafica-t2/blob/master/imagens/loader_professor.png)

Gerado pelo Pipeline:

![alt text](https://github.com/Lucasmq/computacao-grafica-t2/blob/master/imagens/mygl.png )

# Conclusão

Com a implementação do pipeline podemos colocar em prática os conhecimentos obtidos na disciplina e entender melhor o funcionamento.

# Referências

- Notas de Aula do Professor Christian A. P.


