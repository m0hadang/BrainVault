# Super Key

relation 에서 tuples를 unique 하게 식별할 수 있는 attributes set

ex) PLAYER(id, name, team_id, back_number, birth_date) 의 Super Key 들
- {id, name, team_id, back_number, birth_date}
- {id, name}
- {name, team_id, back_number}
- etc...

# Candidate Key

어느 한 attribute 라도 제거하면 unique 하게 tuples 를 식별할 수 없는 super key

ex) PLAYER (id, name, team_id, back_number) 에서 candidate key 는 
- {id}
- {team_id, back_number}

# Primary Key

relation 에서 tuples 를 unique 하게 식별하기 위해 선택된 candidate key

ex) {id} 또는 {team_id, back_number} 가 PK 가 될 수 있다

# Unique Key

primary key 가 아닌 candidate keys (alternate key)

ex) PK를 {id} 로 설정 하였을 경우 UK 는 {team_id, back_number}

# Foreign Key

다른 relation 의 PK 를 참조하는 attributes set

PK 가 없는 values를 FK 값으로 가질 수 없다

