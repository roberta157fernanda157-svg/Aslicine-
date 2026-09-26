# Integração do cadastro de filmes

O formulário envia `POST /api/admin/movies` com JWT de administrador.

Android Emulator: `http://10.0.2.2:3000`

A versão atual usa `--dart-define=ASLICINE_ADMIN_TOKEN=SEU_TOKEN` apenas para teste. Depois o token será armazenado com segurança após o login administrativo.
