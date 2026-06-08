# API Gateway — Gateway de API Acadêmico

Responsável por centralizar, rotear e proteger todas as requisições externas direcionadas aos microsserviços do ecossistema acadêmico. Ele gerencia políticas de CORS globais e realiza a validação de segurança através de um filtro customizado de tokens JWT.

---

## Tecnologias

Java 17 · Spring Boot 4.0.6 · Spring Cloud Gateway Server WebFlux (2025.1.1) · Spring Cloud LoadBalancer · Spring Cloud Netflix Eureka Client · JSON Web Token (jjwt 0.11.5)

---

## Responsabilidades e Rotas

O Gateway intercepta as chamadas na porta `8080`, valida o token de autenticação e redireciona os fluxos via balanceamento de carga (`lb://`) para os seguintes serviços baseados no padrão de URL:

- **Roteamento Público:**
  - `auth-service` (`/api/login/**`) → Direcionado para `PROFESSORES-AUTH`.
- **Roteamento Protegido (Exige filtro de autenticação):**
  - `docentes-service` (`/api/professores/**`, `/api/formacoes/**`, `/api/interesses/**`, `/api/disponibilidades/**`) → Direcionado para `DOCENTES`.
  - `academico-service` (`/api/disciplina/**`, `/api/horario/**`) → Direcionado para `MS-ACADEMICO`.
  - `institucional-service` (`/api/ies/**`, `/api/escolas/**`, `/api/coordenadores/**`) → Direcionado para `MICROSSERVICO-INSTITUCIONAL`.

---

## Mecanismo de Segurança (`AuthenticationFilter`)

O gateway intercepta as rotas protegidas e valida o cabeçalho `Authorization: Bearer <token>`. Caso o JWT seja válido, ele decodifica os claims e injeta novas informações na requisição downstream para consumo dos microsserviços internos:
- `X-User-Id` (Contendo o Subject do token)
- `X-User-Role` (Contendo a Role do usuário)

A assinatura do token é validada usando a propriedade `api.jwt.secret`.

---

## Ordem de inicialização

Deve ser iniciado por último, garantindo que o servidor de descoberta e todos os microsserviços downstream já estejam disponíveis no Eureka:

1. Eureka Server
2. Microsserviços de negócio (ms-docentes, ms-institucional, ms-academico)
3. ms-auth
4. **API Gateway (Este repositório)**

---

## Desenvolvido por:

Grupo 4 - Arquitetura de Software T1 - Universidade Católica do Salvador

Microsserviços e API Gateway: [Link da Lista](https://github.com/stars/eduardaleall/lists/repos-ms-disp)
