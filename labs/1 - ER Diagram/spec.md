Типи:
team_status: "active", "disbanded"
team_member_status: "active", "left", "kicked"
tournament_participant_status: "pending", "accepted", "rejected"

Сутності:

User:
uuid id PK - унікальний ідентифікатор користувача
string email UNIQUE NOT NULL - унікальна електронна пошта
string hashed_password NOT NULL - зашифрований пароль
string nickname UNIQUE NOT NULL - унікальний нікнейм на платформі
string name NULL - ім'я користувача
string description NULL - опис користувача
string avatarURL NULL - аватар користувача

Team:
uuid id PK - унікальний ідентифікатор команди
string name UNIQUE NOT NULL - унікальна назва команди
string avatarURL NULL - аватар команди 
uuid captain_id FK NOT NULL - ідентифікатор капітана 
timestamp created_at NOT NULL - дата та час створення команди
int max_team_players NULL - максимальна кількість учасників
team_status status NOT NULL DEFAULT "active" - статус команди


Team_Member:
uuid team_id FK PK NOT NULL - ідентифікатор команди
uuid user_id FK PK NOT NULL - ідентифікатор користувача (гравця)
timestamp joined_at NOT NULL - дата вступу до команди
team_member_status status NOT NULL DEFAULT "active" - статус учасника команди

Tournament:
uuid id PK - унікальний ідентифікатор турніру
string name NOT NULL - назва турніру
string game NOT NULL - назва гри
uuid organizer_id FK NOT NULL - ідентифікатор організатора
decimal prize_pool - сума призового фонду
int min_team_players NOT NULL - мінімальна кількість гравців у команді для заявки
int max_team_players NULL - максимальна кількість гравців у команді для заявки

Tournament_Application:
uuid id PK - унікальний ідентифікатор заявки
uuid tournament_id FK NOT NULL - ідентифікатор турніру
uuid team_id FK NOT NULL - ідентифікатор команди
tournament_participant_status status NOT NULL DEFAULT "pending" - статус заявки

Tournament_Application_Member:
uuid id PK - унікальний ідентифікатор учасника якого подали на турнір
uuid tournament_application_id FK - унікальний ідентифікатор заявки


Encounter:
uuid id PK - унікальний ідентифікатор зустрічі
uuid tournament_id FK NOT NULL - ідентифікатор турніру
string stage NOT NULL - етап турнірної сітки
uuid team1_id FK NULL - ідентифікатор першої команди
uuid team2_id FK NULL - ідентифікатор другої команди
uuid winner_team_id FK NULL - ідентифікатор команди-переможця
uuid next_encounter_id FK NULL - ідентифікатор наступної зустрічі в сітці

Match:
uuid id PK - унікальний ідентифікатор матча
uuid encounter_id FK NOT NULL - ідентифікатор зустрічі
int map_order NOT NULL - порядковий номер матча в зустрічі
uuid winner_team_id FK NULL - ідентифікатор команди, що виграла матч

Player_Statistic:
uuid id PK - унікальний ідентифікатор запису статистики
uuid match_id FK NOT NULL - ідентифікатор конкретного матчу
uuid user_id FK NOT NULL - ідентифікатор гравця
int kills NOT NULL - кількість вбивств
int deaths NOT NULL - кількість смертей
int assists NOT NULL - кількість асистів

Зв'язки:
Користувач може бути капітаном багатьох команд, але команда має лише одного капітана (1:N). Гравець може входити до багатьох команд, і команда містить багатьох гравців (N:M, реалізовано через Team_Member). Користувач може створити багато турнірів, але турнір має лише одного організатора (1:N). Команда може подати заявку на багато турнірів, і турнір містить багато команд-учасників (N:M, реалізовано через Tournament_Participant). Турнір містить багато зустрічей у сітці, кожна зустріч належить строго одному турніру (1:N). В одній зустрічі беруть участь дві команди та визначається один переможець. Зустріч має посилання на наступну порожню зустріч у сітці для автоматичного просування переможця (1:1). Зустріч складається з одного або кількох окремих матчів/карт (1:N). Кожен матч містить статистику KDA для багатьох гравців, які брали в ньому участь, а один гравець має статистику в багатьох матчах (N:M, реалізовано через Player_Statistic).