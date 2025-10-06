# Introdução

O avanço dos modelos de linguagem (Language Models - LMs) tem revolucionado os sistemas conversacionais, permitindo interações mais naturais e informativas. No entanto, esses modelos, como o T5 e o GPT-3, são treinados em corpora estáticos, o que os torna suscetíveis a obsolescência à medida que o conhecimento mundial evolui. O problema central é o "esquecimento catastrófico" (catastrophic forgetting), onde atualizações para incorporar novos conhecimentos resultam na perda de informações previamente aprendidas, comprometendo a confiabilidade de sistemas conversacionais em cenários reais, como assistentes virtuais ou chatbots informativos.

Essa questão é justificada pela necessidade de manter LMs "ever-changing", capazes de atualizar conhecimentos temporais (ex.: eleições ou transferências esportivas) sem alterar fatos invariantes (ex.: local de nascimento de figuras históricas). De acordo com Jang et al. (2022), o paradigma de Aprendizado Contínuo de Conhecimento (Continual Knowledge Learning - CKL) aborda essa limitação ao propor benchmarks como INVARIANTLAMA, UPDATEDLAMA e NEWLAMA para medir retenção, atualização e aquisição de conhecimento. Lazaridou et al. (2021) reforçam que métodos tradicionais de CL falham em LMs devido à dificuldade em localizar conhecimentos nos parâmetros, justificando abordagens específicas como parameter-expansion para fomentar atualizações contínuas. Essa proposta visa integrar CKL em sistemas conversacionais para melhorar sua adaptabilidade, reduzindo alucinações e aumentando a precisão em respostas dinâmicas.

# Solução Proposta

A solução proposta integra o paradigma de CKL em um sistema conversacional baseado em LMs, utilizando métodos de parameter-expansion (ex.: LoRA e K-Adapter) para atualizações eficientes. O sistema permite pré-treinamento contínuo em corpora novos (D1) sem reiniciar do zero, preservando conhecimentos de D0. A arquitetura é modular, facilitando escalabilidade e integração com ferramentas como Retrieval-Augmented Generation (RAG) para consultas externas.

## Diagrama de Arquitetura


![Diagrama de Arquitetura](Mermaid%20Diagram.svg)

```mermaid
graph TD
    A[Usuário] --> B[Sistema Conversacional]
    B --> C[LM Principal (T5/GPT)]
    C --> D[Módulo de CKL]
    D --> E[Corpus D0 (Pré-treinado)]
    D --> F[Corpus D1 (Novo: CC-RECENTNEWS)]
    D --> G[Atualizador de Parâmetros (LoRA/K-Adapter)]
    G --> H[Benchmark CKL (INVARIANT/UPDATED/NEW LAMA)]
    H --> I[Métrica FUAR]
    I --> J[Feedback Loop para Otimização]
    J --> C
    B --> K[Saída: Resposta Atualizada]
```

## Descrição Textual das Responsabilidades de Cada Módulo

- **LM Principal (T5/GPT)**: Núcleo do sistema, responsável por gerar respostas conversacionais baseadas em prompts do usuário. Mantém parâmetros iniciais congelados para evitar esquecimento catastrófico.
  
- **Módulo de CKL**: Coordena o aprendizado contínuo, integrando dados de D0 (corpus original) e D1 (novos artigos de notícias). Avalia trade-offs via benchmarks (ex.: medir retenção em INVARIANTLAMA) e aciona atualizações periódicas.

- **Atualizador de Parâmetros (LoRA/K-Adapter)**: Expande parâmetros sem alterar o LM original. LoRA adiciona matrizes de baixa rank para eficiência computacional; K-Adapter insere camadas novas para injeção de conhecimento. Responsável por treinar em D1, minimizando FUAR ( Forgotten / (Updated + Acquired) Ratio ).

- **Benchmark CKL e Métrica FUAR**: Avalia desempenho pós-atualização. INVARIANTLAMA verifica retenção; UPDATEDLAMA e NEWLAMA medem atualizações e aquisições. FUAR quantifica eficiência, guiando ajustes no loop de feedback.

- **Feedback Loop para Otimização**: Analisa métricas e ajusta hiperparâmetros (ex.: taxa de aprendizado) para balancear forgetting e learning, reinjetando dados no LM.

Essa arquitetura permite atualizações em batch, com D1 coletado via crawling (ex.: news-please), fomentando CKL sem alto custo computacional.

# Conclusão

A proposta de integrar CKL em sistemas conversacionais representa um avanço prático para LMs dinâmicos, alinhando-se à visão de inteligência natural que atualiza conhecimentos continuamente. Pessoalmente, considero-a viável para mitigar obsolescência, mas destaco desafios como a dependência de corpora de qualidade e o risco de viés em notícias recentes. O esforço para implementação é moderado: requer integração de bibliotecas como Hugging Face para LoRA/K-Adapter, com custo inicial em GPU para treinamento (estimado em horas para D1 pequeno). Em larga escala, parcerias com provedores de nuvem poderiam reduzir barreiras, tornando-a acessível para desenvolvedores independentes.

# Referências Bibliográficas

JANG, J. et al. Towards continual knowledge learning of language models. *arXiv preprint arXiv:2110.03215v4*, 2022.

LAZARIDOU, A. et al. Mind the gap: Assessing temporal generalization in neural language models. In: *Advances in Neural Information Processing Systems*. [S.l.: s.n.], 2021. p. 29348-29363.

RAFFEL, C. et al. Exploring the limits of transfer learning with a unified text-to-text transformer. *Journal of Machine Learning Research*, v. 21, n. 140, p. 1-67, 2020.