# 🔥 Monitor de Calor — Frota Offline

Dashboard web para monitoramento de veículos offline com análise de cobertura de rede móvel (ERBs) por município.

![Status](https://img.shields.io/badge/status-ativo-22c55e) ![Tech](https://img.shields.io/badge/stack-HTML%20%2F%20JS%20%2F%20Leaflet-0ea5e9)

---

## 📋 Sobre o projeto

O Monitor de Calor cruza duas fontes de dados:

1. **Planilha de frota** — veículos que pararam de comunicar, com coordenadas GPS e data da última comunicação
2. **Planilha de ERBs** — estações rádio-base cadastradas pela Anatel, agrupadas por município

A partir desse cruzamento, o sistema identifica se cada veículo está em uma região com ou sem cobertura de rede, ajudando a priorizar ações de campo: visitar primeiro os veículos que têm sinal disponível mas ainda estão offline.

---

## ✨ Funcionalidades

- **Mapa interativo** com marcadores coloridos por urgência (dias offline)
- **Painel de detalhe** por veículo com cobertura confirmada por operadora e tecnologia (2G/3G/4G/5G)
- **Filtros de cobertura** — exibir apenas veículos com ou sem cobertura cadastrada no município
- **Filtros por urgência** — clique nos cards de estatística para filtrar por faixa de dias offline
- **Busca** por placa ou município
- **Geocoding reverso** via Nominatim para veículos sem município informado
- **Atualização automática** a cada 60 segundos
- **Fallback de proxy** — tenta 3 proxies CORS diferentes para garantir leitura das planilhas

---

## 🗂️ Estrutura esperada das planilhas

### Planilha de Frota (Google Sheets publicada como CSV)

| Coluna | Descrição |
|--------|-----------|
| `PLACA` | Placa do veículo |
| `LAT` | Latitude (decimal, aceita vírgula ou ponto) |
| `LONG` / `LNG` / `LON` | Longitude |
| `PAROU DE COMUNICAR` | Data no formato `DD/MM/AAAA` |
| `MUNICIPIO` / `CIDADE` / `MUN` | Nome do município *(opcional — se ausente, usa geocoding reverso)* |

### Planilha de ERBs (Google Sheets publicada como CSV)

| Coluna | Descrição |
|--------|-----------|
| `MUN` / `MUNICIPIO` / `CIDADE` | Nome do município |
| `TECS` / `TECNOLOGIA` | Tecnologias disponíveis: `2G`, `3G`, `4G`, `5G` |
| `OPERADORA` / `OPERATOR` | Nome da operadora |
| `CLASSINFRAFISICA` / `CLASS` | Classificação da infraestrutura *(opcional)* |

> A fonte recomendada para a planilha de ERBs é o [Portal de Dados Abertos da Anatel](https://informacoes.anatel.gov.br/paineis/outorga-e-licenciamento/estacoes-e-areasde-registro).

---

## ⚙️ Como usar

### 1. Configure as planilhas

Publique ambas as planilhas do Google Sheets como CSV:

> Arquivo → Compartilhar → Publicar na web → CSV

### 2. Atualize as URLs no arquivo HTML

No início do `<script>`, substitua as constantes:

```js
const SHEET_FROTA = 'https://docs.google.com/spreadsheets/d/e/SEU_ID_AQUI/pub?gid=0&single=true&output=csv';
const SHEET_ERBS  = 'https://docs.google.com/spreadsheets/d/e/SEU_ID_AQUI/pub?gid=0&single=true&output=csv';
```

### 3. Abra o arquivo

Basta abrir o `monitor_calor.html` diretamente no navegador — não precisa de servidor, build ou dependências instaladas.

---

## 🎨 Legenda visual

| Cor | Significado |
|-----|-------------|
| 🔵 Azul | Offline há ≤ 2 dias |
| 🟡 Amarelo | Offline há 2–7 dias |
| 🔴 Vermelho | Offline há > 7 dias (crítico) |
| 🟢 Verde (centro do marcador) | Município com cobertura de ERB cadastrada |
| ⚪ Cinza (centro do marcador) | Município sem cobertura cadastrada |

---

## 🛠️ Tecnologias

- [Leaflet.js](https://leafletjs.com/) `v1.9.4` — mapa interativo
- [OpenStreetMap](https://www.openstreetmap.org/) — tiles do mapa
- [Nominatim](https://nominatim.org/) — geocoding reverso (coordenadas → município)
- HTML + CSS + JavaScript puro — sem frameworks, sem build

---

## 🔍 Diagnóstico

Se os badges de cobertura aparecerem como `—` para todos os veículos, abra o console do navegador (F12) e verifique:

```
Colunas da planilha de frota: PLACA, LAT, LONG, ...
Coluna de município detectada: MUNICIPIO
```

Se aparecer `NENHUMA`, o nome da coluna de município na sua planilha não foi reconhecido automaticamente. Adicione o nome exato à lista `candidatosMun` no código.

---

## 📄 Licença

MIT — livre para uso e adaptação.
