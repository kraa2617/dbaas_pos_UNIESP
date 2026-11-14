# dbaas_pos_UNIESP
Repositório para entrega da atividade prática da disciplina de DBaaS: Database as a Service


Identificação: 
          - Nome: Kaique Rhuan de Azevedo Albuquerque
          - Cenário: E - Réplica isolada e depois queda do primário
          - Caminho: Via Docker


Etapas:
1. Desligue a réplica (isolada).
<img width="977" height="512" alt="image" src="https://github.com/user-attachments/assets/7514f5fe-2bd6-459a-a9e3-c09a0503b2c3" />
Executado comando -> 'docker compose stop replica' para desligar a réplica.

    
2. No primário: INSERT INTO eventos SELECT FROM generate_series(1, 50); e anote


   
SELECT max(id) FROM eventos; (M1).
4. Anote T0 e derrube o primário.
5. Ligue a réplica e promova-a. Anote T1.
6. No novo primário, anote SELECT max(id) FROM eventos; (M2).
