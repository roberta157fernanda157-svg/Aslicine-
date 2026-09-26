# ASLICINE — Adicionar Filme

Campos do módulo:
- título
- título original
- sinopse
- ano
- duração
- classificação indicativa
- capa
- imagem de fundo
- trailer
- vídeo principal (HLS recomendado)
- idiomas de áudio
- idiomas de legendas
- destaque
- ativo/inativo
- rascunho/publicado

API:
- `GET /api/admin/movies`
- `POST /api/admin/movies`
- `PUT /api/admin/movies/:id`
- `DELETE /api/admin/movies/:id`

Todos os endpoints administrativos exigem autenticação JWT e `role=admin`.

Importante: o sistema não concede direitos de distribuição. Só publique conteúdo para o qual o ASLICINE tenha autorização/licença ou que esteja legalmente disponível em domínio público.
