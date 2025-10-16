# Readme

This is a payment gateway project written in GoLang in compliance with the
PCI DSS v4.0.1 requirements outlined in the following document.

- [PCI DSS v4.0.1](https://blog.pcisecuritystandards.org/just-published-pci-dss-v4-0-1)

### Technologies

Reasons behind choosing certain technologies are stated below

1. **RabbitMQ**: Asynchronous communication between services.

> RabbitMQ offers better go support, has strong security features and is easier
> to setup and work with in go. For PCI DSS compliant systems, RabbitMQ provides
> robust security features such as TLS encryption, authentication, logging, and access
> control.

2. **GoReleaser**: Automated release management for go applications.

> GoReleaser is a tool that automates the release process for Go projects. It
> supports multiple platforms and architectures, and can generate binaries,
> Docker images, and other artifacts. It also supports continuous integration
> and continuous delivery (CI/CD) pipelines, making it easy to integrate into
> existing workflows. Uses semantic versioning by default, and all tags should
> be compliant with the [Semantic Versioning Guidelines](https://semver.org/)

## Requirements

- **SQL Queries**: using sql query builder, [squirrel](https://github.com/Masterminds/squirrel)

  > ORMs are not a good choice for payment gateway processing.

  > SQL query builders are a good choice since they offer input sanitization,
  > readability and support for multiple databases.

- **Database Driver**: [pgx](https://github.com/jackc/pgx)

  > Posgresql database driver, TLS enabled database access. Offers better
  performance compared to `lib/pd`.

- **Cryptography**: using [crypto](https://pkg.go.dev/crypto)

- **Configuration**: using [viper](https://github.com/spf13/viper)

  > Widely adopted, also supports [out-of-bounds](https://learn.microsoft.com/en-us/azure/security/fundamentals/secrets-best-practices)
  stores, like `etcd`, `firestore`, `NATS`.

  > note: doesn't natively support AWS Secrets Manager, can be paired with [aws-secrets-manager](https://docs.aws.amazon.com/sdk-for-go/v2/developer-guide/welcome.html)

- **Messaging**: using [rabbitmq](https://github.com/streadway/amqp), with
RabbitMQ

- **Logging**: using [zap](https://github.com/uber-go/zap)

> Secure, high performance logging library. Can generate structured, auditable
> logs.

- **Networking**: using [gin](https://github.com/gin-gonic/gin)

> Improved routing, middleware support, and error handling.

## Security Practices

1. **Never Store Sensitive Data Unnecessarily:**

- Do not store CHD, or sensitive auth
data unless absolutely required.
- Never log full PANs, CVVs, or sensitive data.

2. **Secure Data in transit and at rest:**

- always use strong encryption, TLS 1.2+, for all data in transit
- encrypt all sensitive data that is stored

3. **Strong Access Controls:**

- enforce authentication and authorization for all payment functions and data
- use the principle of least privilege

4. **Input Validation and Output Encoding:**

- validate all inputs coming from any source
- encode all output to avoid leaking sensitive information

5. **Logging and Monitoring:**

- log all security-relevant events like access, errors, and any suspicious
activity
- never log sensitive data like full PANs, CVV, etc...

6. **Error Handling:**

- do not log sensitive data in error messages
- only log sensitive data in error messages for administrators

7. **Secure Dependencies:**

- keep libraries and dependencies up to date
- use secure versions of all dependencies

8. **Session Management:**

- Use secure, expiring sessions
- Invalidate sessions on logout or timeout

9. **Code Reviews & Testing:**

- conduct code reviews on security & vulnerability
- perform regular security testing

10. **Segregate and Isolate Data:**

- separate pci code and data from non-pci components
- use application level and network level isolation

**Summary:**

| Key Point                    |                                           |
|------------------------------|-------------------------------------------|
| No unnecessary data storage  | Reduces risk of data breach               |
| Encryption                   | Protects data in transit/at rest          |
| Access control               | Prevents unauthorized access              |
| Input validation             | Stops common attacks                      |
| Secure logging               | Enables monitoring, avoids data leaks     |
| Error handling               | Prevents info disclosure                  |
| Secure dependencies          | Reduces risk from third-party code        |
| Session management           | Protects user sessions                    |
| Code review/testing          | Catches issues early                      |
| Segregation                  | Limits PCI scope and exposure             |

## Coding Guideline

1. Naming conventions

- **Directories:** use **lowercase, short, descriptive** names
- **Files:** use lowercase, with underscores if needed
- **Packages:** name after their use-case or purpose

2. Main Entrypoint

- `cmd/server/main.go` is the main entrypoint for the application.
- keep root clean, *allows for multiple binaries*

3. Internal & Public Code

- use `internal/` for code that should **not** be imported by others
- use `pkg/` for reusable libraries/utils

4. Configuration Management

- store config loading logic in `internal/config/`
- use env. variables, external services, or file-based configuration
- note: never hardcode

5. Testing

- place unit tests next to the code (`foo.go`, `foo_test.go` should be in the
same package)
- use `/test` directory for integration tests

6. Security and Compliance

- **Never commit secrets or keys**, use `.gitignore`
- document secure coding practices in `docs/secure_coding_practices.md` **Working in Progress*

10. Consistent Formatting

- use `gofmt` or `goimports` for code formatting
- stick to idiomatic go style

11. Git Commit Conventions

- use the [conventional commit](https://www.conventionalcommits.org/en/v1.0.0/)
style
- structure of the commit message format

> <type>(<scope>): <subject>
> <br>
> [optional! body]
> <br>
> [optional! footer]

- common types of commits

1.  **feat:**        New feature
2.  **fix:**         Bug fix
3.  **docs:**        Documentation changes
4.  **style:**       Formatting, missing semi colons, etc. (no code change)
5.  **refactor:**    Code change that neither fixes a bug nor adds a feature
6.  **test:**        Adding or fixing tests
7.  **chore:**       Maintenance (build, deps, etc.)
8.  **perf:**        Performance improvement
9.  **ci:**          CI/CD related changes
10. **security:**   Security-related changes

- **never include sensitive information in commit messages or code changes**
- explicitly mention security or compliance changes
