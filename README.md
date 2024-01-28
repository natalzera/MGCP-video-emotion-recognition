# MGCP Video Emotion Recognition (Projeto FAPESP 2022/14903-1)

## Introdução

O reconhecimento de emoções a partir de vídeo é um campo desafiador com aplicações em várias áreas. O objetivo á determinar a emoção de segmentos de vídeo utilizando o modelo circumplexo de emoções, que representa valência e ativação. Valência refere-se ao grau de prazer ou desprazer de uma emoção, enquanto o ativação indica a intensidade ou nível de ativação da emoção. A imagem abaixo mostra um exemplo de como são representadas algumas emoções no modelo circumplexo.

![Exemplo modelo circumplexo de emoções](https://github.com/natalzera/MGCP-video-emotion-recognition/blob/main/images/circumplex.png)

## Metodologia

Este projeto propõe um novo método chamado Consenso Pseudo-rotulagem Multimodal Baseado em Grafos (em ingles, Multimodal Graph-based Consensus Pseudolabeling, MGCP) para o reconhecimento de emoções não supervisionado em vídeo. O método combina modelos unimodais pré-treinados para imagens, áudio e texto, aproveitando seu consenso para gerar pseudo-rotulagens e estimar as coordenadas emocionais. O metodo MGCP consiste em três etapas: representar os segmentos de vídeo usando embeddings dos modelos unimodais, construir grafos específicos de cada modalidade com base em similaridade e integra-los em um gráfico multimodal para aprender as coordenadas finais valência-ativação. A imagem abaixo ilustra um exemplo das etapas que o método segue com os canais de áudio e texto apenas, mas na prática também utiliza do canal de imagem.

![Passo a passo do método MGCP](https://github.com/natalzera/MGCP-video-emotion-recognition/blob/main/images/steps.png)

Na etapa final, geramos as pseudo-rotulagens medindo o consenso entre as modalidades e investigamos duas diferentes abordagens para estimar as coordenadas emocionais finais:

- Utilizar de  framework de regularização de grafos que utiliza das pseudo-rotulagens para estimar as coordenadas emocionais finais. Essa técnica está descrita e analizada no artigo publicado, citado abaixo.
- Utilizar uma Graph Convolutional Network (GCN) que utiliza das pseudo-rotulagens para selecionar vértices de treino e usa as embeddings extraídas de cada vértice para treinar e estimar as coordenadas emocionais finais. Essa técnica é elaborada e analizada neste repositório.

### Hiperparâmetros do Modelo

Ao trabalhar com o MGCP para a segunda técnica descrita, deve-se atentar em 3 hiperparâmetros que fazem toda a diferença na execução do modelo e, consequentemente, no seu resultado:
- **ws**: É um vetor contendo 3 valores reais de 0 a 1 que representam a importância considerada para os canais de texto, áudio e imagem respectivamente. Quanto maior o valor para uma modalidade, maior será sua importância no modelo. É importante saber que a soma desses 3 valores deve sempre ser igual a 1;
- **p**: Parâmetro que pode ser definido com valores reais de 0 a 1 que representa o limite mínimo de similaridade entre vértices para haver uma aresta entre eles no grafo. Quanto maior o seu valor, menos conexo fica o grafo. A conexão entre vértices faz com que suas coordenadas sejam influenciadas entre si ao decidir um consenso para as suas coordenadas emocionais finais;
- **t**: Parâmetro que pode ser definido com valores reais de 0 a 1 que representa o limite mínimo de consenso entre as modalidades para o vértice ser do conjunto de treino da GCN. Quanto maior o seu valor, haverá menos vértices de treino para a GCN.

## Avaliação Experimental e Conclusão

Para a segunda técnica elaborada e demonstrada neste repositório, fizemos a avaliação experimental no conjunto de dados Ryerson Audio-Visual Database of Emotional Speech and Song (RAVDESS), que contém um total de 1440 vídeos de 2 a 5 segundos de duração, gravados por 24 atores. Após os testes, percebeu-se que os modelos unimodais utilizados obtiveram certa dificuldade em identificar precisamente as emoções neste conjunto de dados e, como consequência, o método MGCP também. Na imagem abaixo são mostrados os mapas de calor das emoções identificadas pelos modelos em comparação com o mapa de calor esperado (em "Ground Truth") para um conjunto de vídeos de exemplo do dataset.

![Mapas de calor das emoções geradas pelos modelos para um conjunto de vídeos de exemplo](https://github.com/natalzera/MGCP-video-emotion-recognition/blob/main/images/results.png)

Analizando essa imagem, pode-se notar que o canal de texto teve muita pouca utilidade na previsão deste exemplo, enquanto os canais de áudio e imagem conseguiram se direcionar para as emoções esperadas, mas não encontraram a intensidade correta para acertarem as coordenadas. Esses problemas obtidos nos modelos unimodais se refletiram no método proposto que, mesmo podendo ponderar as modalidades, obteve um desempenho rasoável neste conjunto de dados.

Devido à esses problemas encontrados, ao calcular o Erro Quadrático Médio (em inglês, Mean Square Error, MSE) das previsões geradas pelos modelos unimodais e pelo modelo multimodal proposto para os vídeos do conjunto de dados selecionado, percebe-se que os modelos unimodais de Imagem e Texto tiveram um desempenho ruim comparados ao modelo unimodal de Áudio. Dessa forma, o método MGCP proposto acabou possuindo um MSE um pouco maior que o modelo de áudio, como mostra a imagem abaixo.

![MSE calculado para os modelos unimodais e multimodal sobre o conjunto de dados](https://github.com/natalzera/MGCP-video-emotion-recognition/blob/main/images/mse.png)

Portanto, podemos concluir que apesar do método desenvolvido apresentou problemas ao trabalhar com o dataset escolhido devido aos problemas de seus modelos unimodais com o conjunto de dados, é um modelo bastante flexível e pode ser adaptado facilmente a utilizar de outros modelos unimodais pré-treinados para possuir um desempenho melhor. Portanto, o projeto apresentado possibilita diversas oportunidades de melhorias e aplicações em diversas áreas da computação.

## Artigos Publicados

- COUTINHO, G. N. et al. Multimodal audio emotion recognition with graph-based consensus pseudolabeling. In: SBC. Anais do 20th Encontro Nacional de Inteligência Artificial e Computacional, 2023. p. 1–15.
