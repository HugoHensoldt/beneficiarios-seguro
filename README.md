# Previsão de pagamento de sinistros (projeto introdutório)

**Pergunta:** dado um processo de sinistro no momento da abertura, qual a probabilidade de ele ser pago?
E quanto a seguradora deve esperar desembolsar com os processos de um período?

**Foco:** feature engineering com modelos padrão (regressão logística e random forest).

## Como rodar

```
python 01_prepara_base.py   # gera base_processos.csv a partir de ../Experimento_Sabioni.xlsx e ..._inscricao.csv
python 02_modelo.py         # modelos, comparação e desembolso esperado
```

## Base (`base_processos.csv`)

Uma linha por processo: 61.200 processos de sinistro (só capital), avisados entre jan/2017 e jun/2024,
já encerrados. Ficaram de fora resgates, portabilidade, devoluções e previdência. Nome e CPF foram
removidos.

| coluna | descrição |
|---|---|
| id_processo, id_segurado | identificadores anônimos |
| data_fato, data_aviso, data_abertura | quando o sinistro ocorreu, foi avisado e o processo foi aberto |
| fato_gerador | tipo de sinistro (óbito natural, invalidez, ...) |
| negocio | VidaIndividual ou VidaEmGrupo |
| produto_principal, status_produto | produto de maior valor no processo e se ainda é comercializado |
| data_inicio/fim_comercializacao | período de venda do produto |
| data_implantacao | quando a apólice foi contratada; preenchida só para VidaIndividual (em VidaEmGrupo falta em 95% dos casos) |
| n_coberturas, n_produtos | quantas coberturas (inscrição + produto) e produtos o processo envolve |
| valor_reclamado | soma dos capitais das coberturas |
| n_linhas_registro | **não use como variável explicativa** (ver abaixo) |
| valor_pago | total pago, **é desfecho, não variável explicativa** |
| pago | alvo: 1 se valor_pago > 0 |

## Cuidado: vazamento de informação

Só pode entrar no modelo o que se sabe **na abertura do processo**. `n_linhas_registro` conta as linhas
do processo na base original, mas cada pagamento gera linhas novas: processos com 1 linha são pagos em
0,1% dos casos, e processos com 4 linhas em 94%. Usar essa coluna é usar a resposta para prever a
resposta. O modelo parece melhor do que é e não funcionaria na prática.

Também não confirmamos se `n_coberturas` e `n_produtos` estão completos já na abertura. É preciso
confirmar com quem extraiu os dados.
