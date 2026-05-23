# Análise Estrutural da Rede Viária de Canguaretama-RN

**Autor:** José Alex Araújo de Santana  

---

## 🎥 Vídeo de Apresentação

> 🔗 **[Vídeo de Apresentação](https://www.loom.com/share/75c0c071154343798659a5d93f2c7c1c)**

---

## 1. Região Analisada

**Cidade:** Canguaretama, Rio Grande do Norte, Brasil  
**Justificativa:** Canguaretama é a cidade onde o autor reside. A escolha foi intencional para verificar se as métricas de grafos confirmam o conhecimento urbano local. A cidade tem tamanho adequado para análise — evitando o risco de regiões grandes demais citado no enunciado.

A malha viária apresenta dois núcleos urbanos distintos: o centro histórico (maior, a leste) e um bairro secundário (menor, a oeste), conectados por vias rurais esparsas.

---

## 2. Objetivo

Aplicar conceitos de grafos em uma rede real, interpretando a malha viária de Canguaretama como um grafo e identificando:

- Nós centrais da rede (**hubs**);
- Regiões estruturalmente densas via **decomposição k-core**;
- Pontos críticos de fluxo via **métricas de centralidade**;
- Diferenças entre visualização geográfica e estrutural.

> A proposta não é apenas executar bibliotecas, mas compreender como estruturas de dados modelam sistemas reais.

---

## 3. Metodologia

### Ferramentas utilizadas
- **OSMnx** — download da rede viária do OpenStreetMap
- **NetworkX** — cálculo das métricas estruturais
- **Matplotlib / Pandas** — visualizações e tabelas em Python
- **Gephi** — visualizações avançadas (GeoLayout + ForceAtlas2)

### Decisões metodológicas
| Decisão | Justificativa |
|---|---|
| `network_type='drive'` | Apenas vias onde se pode dirigir — foco na mobilidade motorizada |
| Conversão para não-direcionado | Betweenness e k-core exigem análise simétrica; sentido das ruas não importa para a posição topológica |
| Remoção de self-loops | Exigência do algoritmo `core_number` do NetworkX |
| Uso do grafo simples (`nx.Graph`) | Algoritmos de k-core não aceitam MultiGraph |

---

## 4. Construção do Grafo

```python
import osmnx as ox
import networkx as nx

place = 'Canguaretama, Rio Grande do Norte, Brazil'
G = ox.graph_from_place(place, network_type='drive')

G_undirected = ox.convert.to_undirected(G)
G_simple = nx.Graph(G_undirected)
G_simple.remove_edges_from(nx.selfloop_edges(G_simple))
```

| Propriedade | Valor |
|---|---|
| Tipo original | MultiDiGraph (direcionado) |
| Nós | 856 |
| Arestas (direcionado) | 2.288 |
| Arestas (não-direcionado) | 1.219 |

---

## 5. Métricas Calculadas

### 5.1 Grau dos Nós

| Estatística | Valor |
|---|---|
| Mínimo | 1 |
| Máximo | 4 |
| Média | 2,85 |
| Mediana | 3,0 |
| Desvio Padrão | 0,75 |

**Distribuição:**
- Grau 1: 100 nós (terminais de via)
- Grau 2: 25 nós (curvas/bifurcações simples)
- Grau 3: 648 nós (cruzamentos em T — maioria)
- Grau 4: 88 nós (cruzamentos em cruz — hubs)

### 5.2 Hubs — Top 10 por Grau

Todos com grau = 4:

| Rank | Node ID |
|---|---|
| 1 | 616958468 |
| 2 | 616958473 |
| 3 | 616958480 |
| 4 | 2311960516 |
| 5 | 2311960517 |
| 6 | 2311960518 |
| 7 | 2311960526 |
| 8 | 2311960529 |
| 9 | 2311960563 |
| 10 | 2311960566 |

### 5.3 Betweenness Centrality

| Rank | Node ID | Betweenness |
|---|---|---|
| 1 | 4381522284 | 0,2822 |
| 2 | 4381522595 | 0,2490 |
| 3 | 7720310850 | 0,2463 |
| 4 | 6423437489 | 0,2104 |
| 5 | 7496558700 | 0,1918 |

### 5.4 Closeness Centrality

| Rank | Node ID | Closeness |
|---|---|---|
| 1 | 638996957 | 0,0814 |
| 2 | 7720310850 | 0,0811 |
| 3 | 4381522258 | 0,0807 |

### 5.5 Core Number e K-Core

| Propriedade | Valor |
|---|---|
| Core máximo | k = 2 |
| Nós com k = 1 | 121 (14,1%) |
| Nós com k = 2 | 735 (85,9%) |
| Nós do 2-core | 735 |
| Arestas do 2-core | 1.093 |

---

## 6. Visualizações

### Python
| Imagem | Conteúdo |
|---|---|
| `01_grafo_inicial.png` | Malha viária completa de Canguaretama |
| `02_distribuicao_grau.png` | Histograma de grau |
| `03_hubs_grau.png` | Top 10 hubs em vermelho |
| `04_betweenness.png` | Mapa de betweenness (YlOrRd) |
| `05_closeness.png` | Mapa de closeness (viridis) |
| `06_core_distribution.png` | Distribuição de core numbers |
| `07_kcore_mapa.png` | Core number por nó (plasma) |
| `08_kcore_destacado.png` | 2-core em vermelho |
| `09_correlacao_metricas.png` | Scatter grau vs betweenness/closeness |

### Gephi
| Imagem | Conteúdo |
|---|---|
| `gephi_geo_grau.png` | GeoLayout + tamanho por grau + cor por core |
| `gephi_geo_betweenness.png` | GeoLayout + tamanho por betweenness |
| `gephi_geo_kcore.png` | GeoLayout + filtro k-core=2 (735 nós) |
| `gephi_geo_top10pct.png` | GeoLayout + filtro grau=4 (88 nós) |
| `gephi_forceatlas2.png` | ForceAtlas2 + cor por core_number |

---

## 7. Respostas às Questões Analíticas Obrigatórias

### Questão 1 — Os nós com maior grau coincidem com os de maior betweenness?

**Não.** O overlap entre o Top 10 por grau e o Top 10 por betweenness foi **zero** — os dois conjuntos são completamente distintos. Os hubs (grau 4) ficam dentro dos clusters urbanos densos, onde existem muitas rotas alternativas ao redor deles. Os nós de alta betweenness (todos com grau 3) estão em posições de corredor entre regiões distintas da cidade.

---

### Questão 2 — O núcleo identificado pelo k-core coincide com os principais hubs?

**Sim, mas vai muito além.** Todos os 10 hubs pertencem ao 2-core (10/10). Porém, o 2-core tem 735 nós — os hubs representam menos de 1,4% do núcleo. O k-core identifica um conjunto muito maior e mais estruturalmente relevante do que apenas os 10 maiores hubs.

---

### Questão 3 — O que a betweenness revela que o grau não revela?

O grau mede **conectividade local imediata** — quantas ruas chegam em um cruzamento.  
A betweenness mede **posição estratégica global** — quão indispensável é aquele ponto para os caminhos entre diferentes partes da cidade.

O nó com betweenness máxima (0,282) aparece em 28,2% de todos os menores caminhos da rede, mesmo tendo grau 3 — apenas uma conexão a mais que o mínimo. Um nó com grau 4 dentro de um cluster denso pode ter betweenness próxima de zero porque existem muitas rotas alternativas ao seu redor.

**Conclusão:** grau alto é condição necessária mas não suficiente para ser estrategicamente crítico. A posição topológica importa mais que o número de conexões.

---

### Questão 4 — O que muda entre visualização geográfica e estrutural?

**Visualização geográfica (GeoLayout):** a rede assume o formato real de Canguaretama. Os dois clusters aparecem separados geograficamente. É possível identificar bairros, eixos viários e vias rurais. Mostra **onde** as coisas estão.

**Visualização estrutural (ForceAtlas2):** a rede é reorganizada por densidade de conexões, sem respeitar coordenadas. Nós mais conectados e de core mais alto migram para o centro; periféricos ficam nas bordas. Revela **como** as coisas se relacionam — a hierarquia estrutural que a geografia oculta.

As duas perspectivas são complementares e obrigatórias para uma análise completa. A geográfica localiza. A estrutural hierarquiza.

---

### Questão 5 — Existem regiões críticas para mobilidade urbana?

**Sim.** As regiões críticas são:

1. **Corredor norte-sul de alta betweenness** — eixo que atravessa o centro de Canguaretama onde se concentram os nós com betweenness acima de 0,17. Um bloqueio nesse corredor impacta uma fração enorme dos deslocamentos da cidade.

2. **Nós-ponte** — interseções com grau 3 e betweenness no percentil 90+. São simples mas estão em posições de passagem obrigatória. São os pontos mais vulneráveis da rede.

3. **121 nós de k=1** — terminais e ramais sem saída. Qualquer bloqueio na única aresta que os conecta à rede gera isolamento completo daquele trecho.

---

### Questão 6 — A rede é homogênea ou apresenta concentração estrutural?

**Homogênea em grau, concentrada em betweenness.**

O grau é homogêneo: desvio padrão de apenas 0,75, máximo de 4, distribuição dominada pelo valor 3. Não há supernós com dezenas de conexões como em redes sociais.

A betweenness é altamente concentrada: o nó mais crítico aparece em 28,2% dos caminhos mínimos, enquanto a maioria dos nós tem betweenness próxima de zero. Há uma minoria de nós-gargalo com poder de fluxo desproporcional.

Essa combinação — homogeneidade local + concentração global — é característica de redes viárias urbanas de pequeno-médio porte.

---

### Questão 7 — Os resultados fazem sentido considerando o conhecimento urbano da região?

**Sim, completamente.**

- Os **hubs** estão nas regiões da Praça Augusto Severo / Igreja Matriz (centro histórico) e no entorno do Hospital Regional / Estádio Municipal — exatamente os cruzamentos mais movimentados que qualquer morador de Canguaretama reconheceria.

- O **corredor de alta betweenness** coincide com o eixo viário principal que os moradores usam para atravessar a cidade de norte a sul.

- A presença de **dois clusters** separados é geograficamente correta: o centro histórico e o bairro secundário (Sertãozinho/Bosque das Palmeiras) são áreas urbanas distintas, separadas por uma zona de menor densidade.

- Os **121 nós de k=1** nas extremidades rurais correspondem às estradas de acesso a sítios e propriedades rurais que terminam sem saída — algo que qualquer residente da região rural de Canguaretama confirmaria.

---

## 8. Principais Conclusões

1. **Métricas diferentes capturam aspectos complementares.** Grau, betweenness, closeness e k-core identificam conjuntos de nós quase disjuntos — nenhuma métrica sozinha é suficiente.

2. **Overlap zero entre hubs e betweenness** é o resultado mais relevante: os cruzamentos mais conectados não são os gargalos de fluxo. Os gargalos são nós com grau médio em posições de corredor.

3. **K-core máximo = 2** indica rede com redundância básica mas sem profundidade de núcleo. 85,9% dos nós têm pelo menos uma rota alternativa dentro do núcleo; os 14,1% restantes são vulneráveis a isolamento.

4. **Grafos modelam cidades reais com fidelidade.** Todos os resultados quantitativos são geograficamente coerentes com o conhecimento urbano de Canguaretama, validando tanto a metodologia quanto os dados do OpenStreetMap.

5. **Aplicação prática:** gestão de tráfego e manutenção viária deveriam priorizar os nós de alta betweenness (gargalos de fluxo) e os 121 nós de k=1 (vulneráveis a isolamento), não apenas os hubs de maior grau.

---

## 9. Estrutura do Repositório

```
├── notebook.ipynb              # Código completo e funcional
├── data/
│   └── rede_canguaretama.graphml
├── images/
│   ├── 01_grafo_inicial.png
│   ├── 02_distribuicao_grau.png
│   ├── 03_hubs_grau.png
│   ├── 04_betweenness.png
│   ├── 05_closeness.png
│   ├── 06_core_distribution.png
│   ├── 07_kcore_mapa.png
│   ├── 08_kcore_destacado.png
│   ├── 09_correlacao_metricas.png
│   ├── gephi_geo_grau.png
│   ├── gephi_geo_betweenness.png
│   ├── gephi_geo_kcore.png
│   ├── gephi_geo_top10pct.png
│   └── gephi_forceatlas2.png
└── README.md
```

---

*Trabalho desenvolvido individualmente. Disciplina DCA3702 — Estrutura de Dados II.*
