# Roteiro do vídeo técnico — Sprint 3 (5 minutos)

Objetivo da gravação: mostrar o protótipo **funcionando** e explicar **como os componentes se integram**. Grave a tela com o terminal de um lado e o navegador com `docs/dashboard.html` do outro.

Antes de gravar, deixe pronto:
- terminal aberto na raiz do repositório;
- `docs/dashboard.html` já aberto no navegador, com a aba no topo da página;
- README aberto na seção 2 (diagramas), para o trecho de arquitetura.

---

## 0:00 – 0:30 — Abertura e problema

**Tela:** slide ou README no topo.

> "Somos a Equipe 2. O GreenVolt é um sistema de gerenciamento energético para eletropostos. O problema que atacamos nas Sprints 1 e 2 é que eletropostos consomem muita energia em horários caros, sobrecarregam a rede e desperdiçam geração solar. Nesta Sprint saímos da proposta e colocamos o sistema para operar."

## 0:30 – 1:15 — Arquitetura e integração

**Tela:** diagrama de blocos do README (seção 2.1).

Fale apontando o fluxo, sem ler a lista:

> "Três sensores alimentam o controlador: o piranômetro dá a irradiância, o medidor bidirecional dá o estado e a tarifa da rede, e os leitores OCPP dão o SoC de cada carro. O controlador RISC-V é o único que decide: ele comanda o banco de baterias, o disjuntor da rede e o setpoint de potência de cada ponto de recarga. Toda decisão vira uma linha de telemetria."

Mostre o fluxograma (2.2) por uns 10 segundos e resuma a prioridade:

> "A ordem é sempre a mesma: sol primeiro, depois bateria, depois rede, e se ainda faltar potência, racionamento."

## 1:15 – 1:50 — A lógica embarcada

**Tela:** `firmware/decisao_riscv.s`, rolando pelas seções `prioridade_solar`, `prioridade_bateria`, `prioridade_rede`.

> "Essa mesma lógica está escrita em Assembly RISC-V, que é o que roda no microcontrolador do quadro. Tudo em aritmética inteira, potência em watts e SoC em por mil, sem ponto flutuante. O controlador que gerencia a energia da planta é ele mesmo eficiente em energia."

Mostre também `src/controlador.py` lado a lado por 5 segundos:

> "O Python é a mesma máquina de decisão, usada para simular e validar."

## 1:50 – 2:25 — Validação das regras

**Tela:** terminal.

```bash
python3 src/teste_integracao.py
```

> "Cada regra tem um teste. Sol suficiente: não aciona a rede. Horário de ponta: a bateria assume. Queda de rede: opera ilhado. Demanda acima do contrato com o banco na reserva: entra o modo economia. E duas verificações de segurança: a rede nunca passa dos 45 kW contratados e o balanço de potência fecha. Oito de oito."

## 2:25 – 3:05 — A execução de 24 horas

**Tela:** terminal.

```bash
python3 src/simulador.py
```

Aponte o relatório enquanto ele aparece:

> "Isso é um dia inteiro de operação, uma decisão por minuto. 459 kWh entregues em 16 sessões de recarga. 73,5% dessa energia veio do sol, direta ou armazenada. A conta do dia foi R$ 105 contra R$ 394 se tudo viesse da rede — 73% de economia."

Mostre rapidamente `dados/telemetria_24h.csv` aberto:

> "São 1.440 linhas de telemetria, uma por minuto, com irradiância, potência de cada fonte, SoC do banco e modo de operação."

## 3:05 – 4:15 — O painel e os três momentos que provam a integração

**Tela:** `docs/dashboard.html`.

Comece pelos indicadores no topo, depois vá ao gráfico de despacho e conte os três momentos:

**1) Meio-dia — sol cobrindo tudo.**
> "Aqui a área amarela é a carga atendida direto pelo sol. A rede está em standby; foram 521 minutos do dia sem consumir nada da concessionária."

**2) 13h05 — queda de rede.**
> "Nessa faixa vermelha a rede cai. Repare que o azul some e o verde assume: o controlador abriu o disjuntor, baixou o piso de SoC de 20% para 10% e manteve as duas recargas em andamento por 30 minutos. Nenhuma sessão foi interrompida — o carro do CP03 que entrou às 13h01 terminou normalmente às 14h18."

**3) 18h às 21h — horário de ponta.**
> "Na faixa de ponta o sistema troca a rede pelo banco que foi carregado com o excedente do meio-dia. E às 17h48 a demanda passou de 100 kW: o controlador entrou em modo economia e rateou a potência priorizando o carro mais descarregado."

Desça até a tabela de comandos automáticos:

> "Foram 22 comandos automáticos em 24 horas, todos sem operador."

## 4:15 – 4:45 — Sustentabilidade e o limite encontrado

**Tela:** gráfico do banco de baterias (parte inferior).

> "Esse gráfico mostra o ganho e o limite. O banco recuperou 60 kWh de excedente que seriam perdidos — sem ele, a participação renovável cairia de 73,5% para 53,9%. Mas das 13h às 17h o banco fica cheio e 57 kWh de sol ainda são desperdiçados. Isso dimensiona a próxima decisão de projeto: ampliar o armazenamento ou habilitar injeção na rede."

## 4:45 – 5:00 — Fechamento

> "Resumindo: sensores, controlador RISC-V, armazenamento e pontos de recarga operando juntos, com dados de um ciclo completo no repositório para conferência. Código, telemetria, diagramas e o painel estão no GitHub. Obrigado."

---

## Checklist de gravação

- [ ] Rodar `python3 src/simulador.py` antes de gravar, para o painel estar atualizado
- [ ] Fonte do terminal em tamanho legível (14pt ou mais)
- [ ] Áudio testado, sem ruído de fundo
- [ ] Duração final abaixo de 5:00
- [ ] Todos os três integrantes aparecem ou narram algum trecho
- [ ] Link do vídeo colado no README (seção Equipe)
