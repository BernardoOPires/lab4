(i) Introdução

A segurança viária tem impacto humano, econômico e social direto. Este trabalho constrói um painel interativo (Power BI) a partir da base pública de Ocorrências de Acidentes de Trânsito com Vítima — Belo Horizonte (2024). O objetivo é organizar, limpar e enriquecer os dados para possibilitar análises sobre padrões temporais (mês, dia, hora), diferenças por tipo de acidente, condições de tempo e pavimento, distribuição por regionais administrativas e indicadores de severidade (fatalidades).

Para orientar o dashboard, foram definidas as seguintes perguntas de pesquisa (RQs), que serão respondidas com visuais específicos:

Quando os acidentes com vítima e os fatais mais acontecem?

Quais regionais concentram volume e/ou maior severidade?

Tipos mais frequentes também são os mais letais?

Condições de tempo (clima) influenciam frequência/severidade?

O tipo de pavimento está associado à severidade?

Locais sinalizados apresentam menor proporção de fatalidades?

O limite de velocidade permitido se relaciona com gravidade?

A origem do boletim muda o perfil dos registros?

(ii) Metodologia / Descrição da Base

Fonte e escopo. CSV anual “Relação de Ocorrências de Acidentes de Trânsito com Vítima — 2024 (BH)”. Unidade de análise: ocorrência (boletim). Campos relevantes:
NUMERO_BOLETIM, DATA HORA_BOLETIM, DATA_INCLUSAO, TIPO_ACIDENTE, DESC_TIPO_ACIDENTE, COD_TEMPO, DESC_TEMPO, COD_PAVIMENTO, PAVIMENTO, COD_REGIONAL, DESC_REGIONAL, ORIGEM_BOLETIM, LOCAL_SINALIZADO, VELOCIDADE_PERMITIDA, COORDENADA_X, COORDENADA_Y, HORA_INFORMADA, INDICADOR_FATALIDADE, VALOR_UPS, DESCRIÇÃO_UPS, DATA_ALTERACAO_SMSA, VALOR_UPS_ANTIGA, DESCRIÇÃO_UPS_ANTIGA.

Ferramenta. Power BI Desktop (ETL com Power Query; modelagem/visuais com DAX e visuais nativos).

Pré-processamento (Power Query).

Tipagem: datas/horas como Date/Time; categóricos (DESC_TIPO_ACIDENTE, DESC_TEMPO, PAVIMENTO, DESC_REGIONAL, ORIGEM_BOLETIM, LOCAL_SINALIZADO) como texto; NUMERO_BOLETIM como texto; coordenadas como número.

Split de data/hora: DATA HORA_BOLETIM → Data e Hora.

Derivadas temporais: Ano, MesNum, MesNome, DiaSemana, HoraInt (0–23), Faixa2h (janelas de 2h).

Sinalizador de vítima: ComVitimaFlag = if Text.Contains([DESC_TIPO_ACIDENTE],"VITIMA") then "COM VITIMA" else "SEM VITIMA".

Limpeza de rótulos: Trim/Clean em categorias; padronização de “NÃO INFORMADO”.

Qualidade: checagem de duplicidade por NUMERO_BOLETIM, horas válidas e nulos.

Modelagem (DAX — medidas iniciais).

Acidentes = COUNTROWS(Fato)

Com_Vitima = CALCULATE([Acidentes], Fato[ComVitimaFlag]="COM VITIMA")

Obitos = CALCULATE([Acidentes], Fato[INDICADOR_FATALIDADE]="SIM")

Percentual_Com_Vitima = DIVIDE([Com_Vitima],[Acidentes])

Taxa_Obito = DIVIDE([Obitos],[Acidentes])

Plano analítico (ligado às RQs).

Tempo: linha mensal e heatmap dia×faixa de hora (frequência) + Taxa_Obito por horário.

Local: barras por DESC_REGIONAL; avaliação de LOCAL_SINALIZADO; exploração de VELOCIDADE_PERMITIDA por faixas.

Características do evento: barras por DESC_TIPO_ACIDENTE (frequência) e 100% empilhadas com INDICADOR_FATALIDADE (proporção de óbitos).

Condições: cortes por DESC_TEMPO e PAVIMENTO.

Limitações assumidas na V1.

Exposição não observada: contagens por regional podem refletir fluxo/população; trabalhos futuros devem normalizar por população/volume de tráfego.

Coordenadas: COORDENADA_X/Y podem não ser lat/long padrão; mapas de pontos exigem conversão de sistema de referência.

Severidade: INDICADOR_FATALIDADE cobre óbitos; gradações de lesão não modeladas nesta versão.

Calendário: se 2024 estiver parcial, sazonalidade deve ser lida com cautela.

(iii) Resultados

Será preenchido após a construção dos visuais que respondem às RQs (com prints e interpretação objetiva).

(iv) Discussão

Será preenchido após os resultados, com análise crítica (vieses de exposição, limitações do dado, implicações e próximos passos: normalização por população/fluxo, integração de clima/iluminação e tratamento de coordenadas).