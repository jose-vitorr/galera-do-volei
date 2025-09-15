# Galera do Vôlei

Uma plataforma online para conectar praticantes de vôlei, organizar partidas e construir uma comunidade engajada através de um sistema baseado em convites e avaliações mútuas.

## Visão Geral

O **Galera do Vôlei** é uma rede social especializada que conecta jogadores de vôlei através de um ecossistema controlado por convites. A plataforma facilita a organização de partidas, promove a qualidade da comunidade através de avaliações bidirecionais e cria um ambiente confiável para prática esportiva.

### Principais Diferenciais

- **Comunidade Curada**: Entrada apenas por convite garante qualidade dos membros
- **Avaliações Bidirecionais**: Organizadores e participantes se avaliam mutuamente
- **Flexibilidade Total**: Partidas mistas, por gênero ou categoria específica
- **Gestão Inteligente**: Workflow automatizado para participação e aprovação
- **Transparência**: Sistema de rankings baseado em performance real

---

## Jornada do Organizador (Moderador)

O organizador é responsável por criar e gerenciar partidas, além de manter a qualidade da comunidade.

### Fluxo Principal

1. **Convite para Plataforma**
   - Recebe convite por email/WhatsApp
   - Realiza cadastro com dados pessoais e categoria de jogo

2. **Criação de Partida**
   - Define local, data/hora e número de participantes
   - Configura categoria (iniciante, intermediário, avançado) e restrições de gênero
   - Estabelece regras de pagamento (opcional)

3. **Gestão de Solicitações**
   - Visualiza pedidos de participação dos jogadores
   - Aprova ou rejeita com base no perfil e histórico
   - Monitora status de pagamentos (quando aplicável)

4. **Durante e Pós-Partida**
   - Marca partida como "Em Andamento" e depois "Realizada"
   - Avalia cada participante (pontualidade, comportamento, habilidade)
   - Recebe avaliações dos participantes sobre sua organização

5. **Evolução na Plataforma**
   - Constrói reputação através de avaliações positivas
   - Aparece nos rankings de melhores organizadores
   - Ganha credibilidade para organizar partidas maiores

---

## Jornada do Jogador (Participante)

O jogador busca partidas compatíveis com seu perfil e participa da comunidade.

### Fluxo Principal

1. **Entrada na Comunidade**
   - Recebe convite de membro existente
   - Completa perfil com categoria, preferências e dados pessoais

2. **Descoberta de Partidas**
   - Navega por partidas disponíveis usando filtros (localização, data, categoria)
   - Visualiza detalhes: organizador, participantes confirmados, avaliações
   - Verifica compatibilidade com suas habilidades e agenda

3. **Solicitação de Participação**
   - Demonstra interesse em participar da partida
   - Aguarda aprovação do organizador
   - Realiza pagamento se necessário

4. **Participação Ativa**
   - Recebe confirmação e detalhes finais
   - Participa da partida presencialmente
   - Interage com outros membros da comunidade

5. **Avaliação e Feedback**
   - Avalia a organização da partida (pontualidade, local, gestão)
   - Recebe avaliação do organizador
   - Constrói histórico e reputação na plataforma

6. **Ciclo Contínuo**
   - Usa histórico para encontrar organizadores confiáveis
   - Convida novos jogadores para expandir a comunidade
   - Pode evoluir para organizador de partidas

---

## Detalhamento de Requisições da API

### Autenticação

| Endpoint | Método | Descrição | Request Body | Response | Status |
|----------|---------|-----------|--------------|----------|--------|
| `/auth/register` | POST | Registro via convite | `{ invitationId, name, email, password, phone?, gender, birthDate, category }` | `{ token, user, expiresAt }` | 201 |
| `/auth/login` | POST | Login do usuário | `{ email, password }` | `{ token, user, expiresAt }` | 200 |
| `/auth/refresh` | POST | Renovar token | `{ refreshToken }` | `{ token, expiresAt }` | 200 |
| `/auth/logout` | POST | Logout (invalidar token) | - | `{ message }` | 200 |

### Gestão de Usuários

| Endpoint | Método | Descrição | Request Body | Response | Status |
|----------|---------|-----------|--------------|----------|--------|
| `/users/profile` | GET | Buscar perfil próprio | - | `User` | 200 |
| `/users/profile` | PUT | Atualizar perfil | `{ name?, phone?, gender?, category? }` | `User` | 200 |
| `/users/search` | GET | Buscar usuários | Query: `name?, category?` | `{ users[], pagination }` | 200 |
| `/users/{id}` | GET | Ver perfil público | - | `{ user, stats }` | 200 |
| `/users/{id}/reviews` | GET | Ver avaliações recebidas | - | `{ reviews[], averageRating }` | 200 |

### Sistema de Convites

| Endpoint | Método | Descrição | Request Body | Response | Status |
|----------|---------|-----------|--------------|----------|--------|
| `/invitations` | POST | Criar convite | `{ email, phone?, message? }` | `Invitation` | 201 |
| `/invitations/sent` | GET | Listar convites enviados | Query: `status?` | `{ invitations[], pagination }` | 200 |
| `/invitations/{id}` | GET | Detalhes do convite | - | `Invitation` | 200 |
| `/invitations/{id}` | DELETE | Cancelar convite | - | - | 204 |
| `/invitations/validate` | POST | Validar convite | `{ invitationId }` | `{ valid, invitation }` | 200 |

### Gestão de Partidas

| Endpoint | Método | Descrição | Request Body | Response | Status |
|----------|---------|-----------|--------------|----------|--------|
| `/matches` | POST | Criar partida | `{ title, description?, location, scheduledAt, category, genderRestriction, maxParticipants, minParticipants, payment? }` | `Match` | 201 |
| `/matches` | GET | Listar partidas | Query: `category?, date?, location?, status?, page?, limit?` | `{ matches[], pagination }` | 200 |
| `/matches/{id}` | GET | Detalhes da partida | - | `{ match, participants[], organizer }` | 200 |
| `/matches/{id}` | PUT | Atualizar partida | `{ title?, description?, scheduledAt?, status?, maxParticipants?, minParticipants? }` | `Match` | 200 |
| `/matches/{id}` | DELETE | Cancelar partida | - | - | 204 |
| `/matches/my` | GET | Minhas partidas organizadas | Query: `status?` | `{ matches[], pagination }` | 200 |

### Participação em Partidas

| Endpoint | Método | Descrição | Request Body | Response | Status |
|----------|---------|-----------|--------------|----------|--------|
| `/matches/{id}/join` | POST | Solicitar participação | `{ message? }` | `Participation` | 201 |
| `/matches/{id}/leave` | DELETE | Cancelar participação | - | - | 204 |
| `/matches/{id}/participants` | GET | Listar participantes | - | `{ participants[], waitingList[] }` | 200 |
| `/matches/{id}/participants/{participationId}` | PUT | Aprovar/Rejeitar solicitação | `{ status, reason? }` | `Participation` | 200 |
| `/participations/my` | GET | Minhas participações | Query: `status?, page?, limit?` | `{ participations[], pagination }` | 200 |
| `/participations/{id}/payment` | POST | Confirmar pagamento | `{ paymentProof?, transactionId? }` | `{ success }` | 200 |

### Sistema de Avaliações

| Endpoint | Método | Descrição | Request Body | Response | Status |
|----------|---------|-----------|--------------|----------|--------|
| `/matches/{id}/evaluate` | POST | Avaliar organização | `{ organizerRating, comment?, categories: { punctuality, organization, communication, location } }` | `{ success, message }` | 201 |
| `/participations/{id}/evaluate` | POST | Avaliar participante | `{ playerRating, comment?, categories: { skill, behavior, punctuality, teamwork } }` | `{ success, message }` | 201 |
| `/evaluations/received` | GET | Avaliações recebidas | Query: `type?, page?, limit?` | `{ evaluations[], stats }` | 200 |
| `/evaluations/given` | GET | Avaliações dadas | Query: `type?, page?, limit?` | `{ evaluations[], stats }` | 200 |
| `/evaluations/pending` | GET | Avaliações pendentes | - | `{ pendingEvaluations[] }` | 200 |

### Rankings e Estatísticas

| Endpoint | Método | Descrição | Request Body | Response | Status |
|----------|---------|-----------|--------------|----------|--------|
| `/rankings/organizers` | GET | Ranking de organizadores | Query: `period?, limit?` | `{ organizers[], period, updatedAt }` | 200 |
| `/rankings/players` | GET | Ranking de jogadores | Query: `category?, period?, limit?` | `{ players[], period, updatedAt }` | 200 |
| `/stats/user/{id}` | GET | Estatísticas do usuário | - | `{ matchesPlayed, matchesOrganized, averageRatings, completionRate, badges[] }` | 200 |
| `/stats/platform` | GET | Estatísticas da plataforma | - | `{ totalUsers, totalMatches, totalInvitations, growthMetrics }` | 200 |

### Notificações

| Endpoint | Método | Descrição | Request Body | Response | Status |
|----------|---------|-----------|--------------|----------|--------|
| `/notifications` | GET | Listar notificações | Query: `unread?, page?, limit?` | `{ notifications[], pagination }` | 200 |
| `/notifications/{id}/read` | PUT | Marcar como lida | - | `{ success }` | 200 |
| `/notifications/read-all` | PUT | Marcar todas como lidas | - | `{ success }` | 200 |
| `/notifications/settings` | GET | Configurações de notificação | - | `{ emailEnabled, pushEnabled, categories }` | 200 |
| `/notifications/settings` | PUT | Atualizar configurações | `{ emailEnabled?, pushEnabled?, categories? }` | `{ settings }` | 200 |

### Localização e Locais

| Endpoint | Método | Descrição | Request Body | Response | Status |
|----------|---------|-----------|--------------|----------|--------|
| `/locations/search` | GET | Buscar locais | Query: `query, lat?, lng?, radius?` | `{ locations[] }` | 200 |
| `/locations/favorites` | GET | Locais favoritos | - | `{ locations[] }` | 200 |
| `/locations/favorites` | POST | Adicionar local favorito | `{ locationId, name?, notes? }` | `{ success }` | 201 |
| `/locations/favorites/{id}` | DELETE | Remover dos favoritos | - | - | 204 |
| `/locations/popular` | GET | Locais mais utilizados | Query: `region?` | `{ locations[], usage }` | 200 |

---
