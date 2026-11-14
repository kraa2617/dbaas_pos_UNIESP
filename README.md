# dbaas_pos_UNIESP
Repositório para entrega da atividade prática da disciplina de DBaaS: Database as a Service


Identificação: 
          - Nome: Kaique Rhuan de Azevedo Albuquerque
          - Cenário: E - Réplica isolada e depois queda do primário
          - Caminho: Via Docker


Etapas:
1. Desligue a réplica (isolada).
<img width="962" height="71" alt="image" src="https://github.com/user-attachments/assets/03fc64e6-1bde-40b6-ae2f-e86f9805a1f3" />

    
2. No primário: INSERT INTO eventos SELECT FROM generate_series(1, 50); e anote SELECT max(id) FROM eventos; (M1).
<img width="961" height="226" alt="image" src="https://github.com/user-attachments/assets/9b2b5ceb-7be4-460b-9c62-aa2cf8632558" />

*M1 = 50*

3. Anote T0 e derrube o primário.
*T0 = 10:46:45*
<img width="965" height="56" alt="image" src="https://github.com/user-attachments/assets/86aac613-d596-4290-b3af-82b05aec70a4" />

  
5. Ligue a réplica e promova-a. Anote T1.
<img width="962" height="147" alt="image" src="https://github.com/user-attachments/assets/8919ced0-afd1-4da0-b89c-bf0dd22a5680" />
*T1 = 10:48:06*

6. No novo primário, anote SELECT max(id) FROM eventos; (M2).
<img width="949" height="83" alt="image" src="https://github.com/user-attachments/assets/0b896d92-84ca-4a07-b347-0313e8c5041f" />

*M2 = 0*

Meça. RPO = M1 − M2 linhas. RTO = T1 − T0.
*RPO = 50 - 0 = 50 linhas*
*RTO = 10:48:06 - 10:46:45 = 00:01:21 (1 minuto e 21 segundos)*

*Corrija*. Refaça o ambiente (Passo 0 da preparação) e ligue a replicação síncrona no primário:
