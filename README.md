# dbaas_pos_UNIESP

Repositório para entrega da atividade prática da disciplina de DBaaS: Database as a Service

### 1. Identificação: 

          - Nome: Kaique Rhuan de Azevedo Albuquerque
          - Cenário: E - Réplica isolada e depois queda do primário
          - Caminho: Via Docker

### 2. Resumo do incidente: 

          No primeiro momento houve uma desconexão da réplica do primário. Devido configuração inicial dos bancos, a escrita assíncrona permitiu que dados continuassem sendo inseridos no banco principal sem que houvesse sinalização da desconexão da réplica. Após isso, o primário foi derrubado. Quando a réplica assume, nota-se perda de dados. 

### 3. Linha do tempo:          

          Horário          |          Ação                                 |          Resultado
          10:44:00         |          Réplica Desligado                    |          Não recebe mais commits
          10:45:00         |          Primário Recebe Dados                |          M1 - Primário com novos dados
          10:46:45         |          Primário Derrubado                   |          T0 - Início do Incidente
          10:48:06         |          Réplica Reiniciada e promovida       |          T1 - Fim do Incidente
          10:48:10         |          Réplica atua como primário           |          M0 - Réplica com dados anteriores ao desligamento

### 4. Medições
          
          RPO = 50 - 0 = 50 linhas
          
          RTO = 10:48:06 - 10:46:45 = 00:01:21 (1 minuto e 21 segundos)

          'Evidência em código nas etapas no final.'

### 5. Causa Raíz

           Ataque derruba réplica que não recebe mais dados novos. Posteriormente primário é derrubado, 
           quando a réplica assume dados são perdidos.
           
### 6. Correção aplicada e a prova de que funcionou (antes e depois).

          Refaça o ambiente (Passo 0 da preparação) e ligue a replicação síncrona no primário:

          Evidência: Script para verificação do estado de sincronismo da réplica retorna "sync" ao invés de "async"
<img width="964" height="98" alt="image" src="https://github.com/user-attachments/assets/7374378c-3426-4418-9848-51ab4eaf790d" />

          Desligue a réplica e tente INSERT INTO eventos DEFAULT VALUES; no primário: o comando
trava. Cancele com Ctrl+C e reverta:

          Evidência: Após comando de parada da réplica, tentativa de escrita no primário travou. Após parar com o Ctrl+C mensagem avisando que não foi possível subir alteração para réplica, apesar de ter alterado no primário.
<img width="961" height="159" alt="image" src="https://github.com/user-attachments/assets/c0a34ebb-a2cf-4c09-96f0-0e198a617beb" />

### 7. Comparação com serviço gerenciado

          Topologias Multi-AZ com réplica síncrona e failover automático seria uma prevenção ao incidente ocorrido, minimizando RPO ao custo de uma maior latência na escrita dos dados.

### 8. Prevenção e lições

          A principal lição tirada deste incidente é sempre verificar a integridade da réplica, criando mecanismos de logs e alarmes para sinalizar estado de saúde do banco. Além da correção aplicada que é o ajuste para uma leitura síncrona, que evita perda de dados.

### 9. Perguntas:
          1. Com síncrona, qual o RPO e o que se paga (latência, disponibilidade)? 
          2. Em que sistema usaria síncrona e em qual assíncrona? 
          3. Que alarme avisaria que a réplica desconectou?

          R.1 - RPO é o mínimo possível, mas se paga com latência de escrita. 
          A sincronicidade exige que o dado esteja disponível nos dois serviços (primário e réplica). 

          R.2 - A síncrona deve ser utilizada em cargas críticas, em que perda de pacotes não é tolerável. 
          Assíncrona em cargas analíticas, como em bancos de verificação ou dashboards internos.

          R.3 - Um alarme com disparo sobre lag de replicação. Com a queda da réplica ou há a suspensão da 
          amostragem da métrica de lag, ou esse valor é extremamente alto.


           
## Etapas:
### 1. Desligue a réplica (isolada).
   
          Evidência: Comando Indicando o desligamento da réplica
<img width="962" height="71" alt="image" src="https://github.com/user-attachments/assets/03fc64e6-1bde-40b6-ae2f-e86f9805a1f3" />

    
### 2. No primário: INSERT INTO eventos SELECT FROM generate_series(1, 50); e anote SELECT max(id) FROM eventos; (M1).
   
<img width="961" height="226" alt="image" src="https://github.com/user-attachments/assets/9b2b5ceb-7be4-460b-9c62-aa2cf8632558" />

          Anotando M1 = 50

### 3. Anote T0 e derrube o primário.

          Anotando T0 = 10:46:45
<img width="965" height="56" alt="image" src="https://github.com/user-attachments/assets/86aac613-d596-4290-b3af-82b05aec70a4" />

  
### 4. Ligue a réplica e promova-a. Anote T1.
<img width="962" height="147" alt="image" src="https://github.com/user-attachments/assets/8919ced0-afd1-4da0-b89c-bf0dd22a5680" />

          Anotando T1 = 10:48:06

### 5. No novo primário, anote SELECT max(id) FROM eventos; (M2).
<img width="949" height="83" alt="image" src="https://github.com/user-attachments/assets/0b896d92-84ca-4a07-b347-0313e8c5041f" />

          Anotando M2 = 0          

