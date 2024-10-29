Tarefas
-------------------------
1. Identifique as chaves primárias e estrangeiras necessárias para garantir a integridade referencial. Defina-as corretamente.
Tabela tenant
CREATE TABLE tenant (
    id SERIAL PRIMARY KEY,                -- Chave primária para identificar cada cliente de forma única
    name VARCHAR(100) UNIQUE NOT NULL,    -- Nome único para identificar cada cliente
    description VARCHAR(255)
);
Chave Primária (id): Garante que cada cliente seja identificado de forma única no sistema.
Índice Único (name): Adicionado para identificar clientes de forma eficiente.

Tabela person
CREATE TABLE person (
    id SERIAL PRIMARY KEY,                -- Chave primária para identificação única de pessoa
    name VARCHAR(100) NOT NULL,
    birth_date DATE,
    metadata JSONB
);
Chave Primária (id): Cada pessoa possui um identificador único necessário para a integridade nas referências a matrículas (enrollment).

Tabela institution
CREATE TABLE institution (
    id SERIAL PRIMARY KEY,                -- Chave primária para identificação única de instituição
    tenant_id INTEGER NOT NULL REFERENCES tenant(id) ON DELETE CASCADE,  -- Relaciona a instituição ao cliente
    name VARCHAR(100) NOT NULL,
    location VARCHAR(100),
    details JSONB
);
Chave Primária (id): Garante que cada instituição seja única.
Chave Estrangeira (tenant_id): Relaciona a instituição a um cliente. ON DELETE CASCADE remove instituições associadas quando o tenant é excluído.

Tabela course
CREATE TABLE course (
    id SERIAL PRIMARY KEY,                -- Chave primária para identificação única de curso
    tenant_id INTEGER NOT NULL REFERENCES tenant(id) ON DELETE CASCADE,  -- Relaciona o curso ao cliente
    institution_id INTEGER NOT NULL REFERENCES institution(id) ON DELETE CASCADE,  -- Relaciona o curso à instituição
    name VARCHAR(100) NOT NULL,
    duration INTEGER,
    details JSONB
);
Chave Primária (id): Identifica cada curso de forma única.
Chave Estrangeira (tenant_id): Assegura que cada curso pertence a um cliente.
Chave Estrangeira (institution_id): Associa o curso a uma instituição, com ON DELETE CASCADE para evitar registros órfãos ao excluir uma instituição.

Tabela enrollment
CREATE TABLE enrollment (
    id SERIAL PRIMARY KEY,                -- Chave primária para identificação única de matrícula
    tenant_id INTEGER NOT NULL REFERENCES tenant(id) ON DELETE CASCADE,  -- Relaciona a matrícula ao cliente
    institution_id INTEGER NOT NULL REFERENCES institution(id) ON DELETE CASCADE,  -- Relaciona a matrícula à instituição
    person_id INTEGER NOT NULL REFERENCES person(id) ON DELETE CASCADE,  -- Relaciona a matrícula à pessoa
    enrollment_date DATE NOT NULL,
    status VARCHAR(20) NOT NULL
);
Chave Primária (id): Assegura que cada matrícula é identificável de forma única.
Chaves Estrangeiras:
tenant_id: Relaciona a matrícula ao cliente, removendo automaticamente as matrículas associadas ao excluir um tenant.
institution_id: Assegura que cada matrícula é associada a uma instituição.
person_id: Vincula a matrícula a uma pessoa, com ON DELETE CASCADE para excluir matrículas caso a pessoa seja removida.


------------------
2. Construa índices que consideras essenciais para operações básicas do banco e de consultas possíveis para a estrutura sugerida.

TABELA TENANT
Para a tabela tenant, onde o campo name pode ser frequentemente consultado:
-- Índice no campo 'name' para acelerar buscas de clientes por nome
CREATE INDEX idx_tenant_name ON tenant (name);
Esse índice será útil em consultas que busquem por nome de clientes, permitindo uma busca rápida.

TABELA PERSON
Para a tabela person, onde consultas por id, name, e metadata JSONB são esperadas:
-- Índice para busca rápida por nome de pessoa
CREATE INDEX idx_person_name ON person (name);
-- Índice no campo JSONB 'metadata' para pesquisas específicas por chaves
CREATE INDEX idx_person_metadata ON person USING GIN (metadata);
idx_person_name: Acelera consultas que filtram registros por nome, que pode ser comum em sistemas de cadastro.
idx_person_metadata: O índice GIN (Generalized Inverted Index) é essencial para melhorar a performance de consultas JSONB que realizam busca por chaves específicas no campo metadata.

TABELA INSTITUTION
Para a tabela institution, onde frequentemente serão feitas consultas com tenant_id e location:
-- Índice no campo 'tenant_id' para acelerar consultas por cliente
CREATE INDEX idx_institution_tenant_id ON institution (tenant_id);
-- Índice no campo 'location' para buscas por localização
CREATE INDEX idx_institution_location ON institution (location);
-- Índice no campo JSONB 'details' para consultas por chaves específicas
CREATE INDEX idx_institution_details ON institution USING GIN (details);
idx_institution_tenant_id: Facilita consultas que agrupam ou filtram instituições por cliente.
idx_institution_location: Otimiza consultas baseadas em localização.
idx_institution_details: O índice GIN melhora a performance de consultas JSONB que verificam chaves específicas no campo details.

TABELA COURSE
Para a tabela course, onde consultas por tenant_id, institution_id, e name podem ser comuns:
-- Índice para consultas por cliente (tenant)
CREATE INDEX idx_course_tenant_id ON course (tenant_id);
-- Índice para acelerar buscas de cursos por instituição
CREATE INDEX idx_course_institution_id ON course (institution_id);
-- Índice no campo 'name' para acelerar buscas de cursos por nome
CREATE INDEX idx_course_name ON course (name);
-- Índice GIN no campo JSONB 'details' para facilitar consultas em dados estruturados
CREATE INDEX idx_course_details ON course USING GIN (details);
idx_course_tenant_id: Acelera consultas baseadas em tenant_id.
idx_course_institution_id: Melhora o desempenho ao buscar cursos específicos de uma instituição.
idx_course_name: Facilita buscas pelo nome do curso.
idx_course_details: O índice GIN auxilia consultas detalhadas baseadas em chaves JSONB no campo details.

TABELA ENROLLMENT
Para a tabela enrollment, onde é essencial otimizar consultas por tenant_id, institution_id, person_id, enrollment_date, e status:
-- Índice para acelerar consultas por cliente (tenant)
CREATE INDEX idx_enrollment_tenant_id ON enrollment (tenant_id);

-- Índice para buscas rápidas por instituição
CREATE INDEX idx_enrollment_institution_id ON enrollment (institution_id);

-- Índice para busca por pessoa específica
CREATE INDEX idx_enrollment_person_id ON enrollment (person_id);

-- Índice combinado para consultas por data de matrícula e status
CREATE INDEX idx_enrollment_date_status ON enrollment (enrollment_date, status);
idx_enrollment_tenant_id: Facilita consultas multi-tenant, acelerando a filtragem por cliente.
idx_enrollment_institution_id: Otimiza consultas que retornam matrículas associadas a uma instituição.
idx_enrollment_person_id: Acelera buscas por pessoa específica em enrollment.
idx_enrollment_date_status: Um índice composto que melhora a performance de consultas que buscam por enrollment_date e status, comuns para relatórios ou rastreamento de matrículas.

Justificativa das Escolhas:
Esses índices foram selecionados para otimizar o desempenho de consultas comuns em um sistema multi-tenant, levando em consideração as especificidades das tabelas e o uso frequente de tenant_id para segmentar dados por cliente. Índices GIN foram usados em campos JSONB para melhorar a eficiência em consultas JSON estruturadas, que tendem a ser mais lentas sem uma indexação especializada. Os índices compostos foram criados quando necessários para cobrir consultas com múltiplos filtros.

-------------
3. Considere que em enollment só pode existir um único person_id por tenant e institution. Mas institution poderá ser nulo. Como garantir a integridade desta regra?

Para garantir a integridade da regra onde um person_id pode aparecer apenas uma vez por tenant e institution na tabela enrollment, mas permitindo que institution_id seja nulo, podemos usar uma restrição de unicidade parcial. No PostgreSQL, é possível criar uma restrição de unicidade condicional utilizando a cláusula UNIQUE com uma expressão WHERE.

Esta abordagem irá:
Restringir a combinação de tenant_id, person_id, e institution_id, permitindo a unicidade apenas quando institution_id não é nulo.
Permitir múltiplas entradas para um person_id com tenant_id iguais quando institution_id é nulo.
Implementação da Regra de Unicidade
No PostgreSQL, a seguinte instrução SQL define a restrição de unicidade parcial para a tabela enrollment:
-- Restrição de unicidade para garantir um único person_id por tenant e institution
CREATE UNIQUE INDEX idx_unique_enrollment_person_tenant_institution
ON enrollment (tenant_id, person_id, institution_id)
WHERE institution_id IS NOT NULL;

Explicação da Solução:
Índice de Unicidade Parcial: O índice idx_unique_enrollment_person_tenant_institution aplica a regra de unicidade para a combinação de tenant_id, person_id, e institution_id apenas quando institution_id tem um valor.

Permissão para Valores Nulos: Ao permitir institution_id como NULL, a restrição não se aplica quando institution_id é nulo, o que significa que múltiplas linhas com o mesmo tenant_id e person_id são permitidas quando institution_id é NULL.

Integridade de Dados: Essa abordagem evita que haja mais de uma combinação de tenant_id, person_id e institution_id para valores de institution_id diferentes de NULL, mantendo a integridade da regra.

-----------------
4. Caso eu queira incluir conceitos de exclusão lógica na tabela enrollment. Como eu poderia fazer? Quais as alterações necessárias nas definições anteriores?


Para implementar exclusão lógica na tabela enrollment, é comum adicionar uma coluna que indique se o registro está "ativo" ou "excluído", em vez de remover o registro fisicamente do banco de dados. Isso permite manter o histórico de registros sem precisar restaurar dados excluídos.

Aqui estão os passos e alterações necessárias para incluir a exclusão lógica na tabela enrollment:
1. Alteração na Estrutura da Tabela
Adicione uma coluna chamada is_active (ou um nome similar), que será um BOOLEAN ou outro tipo de dado que identifique o status do registro.
ALTER TABLE enrollment
ADD COLUMN is_active BOOLEAN DEFAULT TRUE;
is_active BOOLEAN: Essa coluna sinaliza se o registro está ativo (TRUE) ou excluído (FALSE).
DEFAULT TRUE: Define o valor padrão como TRUE, indicando que os registros são inicialmente considerados "ativos".

2. Ajuste dos Índices e Restrições de Unicidade
Para manter a integridade das restrições de unicidade e garantir que um person_id só pode existir uma vez por tenant e institution enquanto estiver ativo, o índice de unicidade parcial precisa ser atualizado para considerar apenas registros ativos. Isso evita conflitos com registros que foram logicamente excluídos.

-- Atualiza o índice de unicidade para considerar apenas registros ativos
DROP INDEX IF EXISTS idx_unique_enrollment_person_tenant_institution;

CREATE UNIQUE INDEX idx_unique_enrollment_person_tenant_institution
ON enrollment (tenant_id, person_id, institution_id)
WHERE institution_id IS NOT NULL AND is_active = TRUE;

3. Atualização de Consultas para Considerar is_active
Modifique as consultas de leitura para buscar apenas registros ativos (is_active = TRUE), a menos que queira incluir também registros logicamente excluídos.

Exemplo de Consultas
Para buscar apenas registros ativos:

SELECT * FROM enrollment
WHERE tenant_id = 1 AND is_active = TRUE;
Para exclusão lógica de um registro, basta atualizar o valor de is_active para FALSE:
UPDATE enrollment
SET is_active = FALSE
WHERE id = 12345;

4. Justificativa das Alterações
Integridade de Dados: A restrição de unicidade foi ajustada para considerar apenas registros ativos, evitando que um person_id seja adicionado novamente ao mesmo tenant e institution enquanto outro registro ativo existe para esses campos.
Manutenção de Histórico: A exclusão lógica permite que os dados antigos sejam preservados para auditoria ou histórico.
Facilidade de Consulta: A atualização das consultas para verificar is_active garante que os registros inativos não sejam incluídos em consultas de leitura padrão, a menos que seja explicitamente desejado.
Com essas mudanças, a exclusão lógica é implementada mantendo a integridade e a eficiência das consultas, atendendo ao requisito de preservação do histórico.




-----------------------------
5. Construa uma consulta que retorne o número de matrículas por curso em uma determinada instituição.Filtre por tenant_id e institution_id obrigatoriamente. Filtre também por uma busca qualquer -full search - no campo metadata da tabela person que contém informações adicionais no formato JSONB. Considere aqui também a exclusão lógica e exiba somente registros válidos.

Para construir uma consulta que retorne o número de matrículas por curso em uma instituição específica, filtrando por tenant_id, institution_id e também por um termo de pesquisa no campo metadata da tabela person, e considerando apenas registros ativos, podemos combinar várias condições:

Filtro por tenant_id e institution_id: Esses são filtros obrigatórios.
Filtro no campo metadata: Utilizaremos o operador @> para buscar o termo em JSONB, que é ideal para uma busca por chave-valor no PostgreSQL.
Exclusão lógica: Filtramos apenas registros de matrícula (enrollment) com is_active = TRUE.
Estrutura da Consulta
A consulta, então, seria algo como:

SELECT 
    c.id AS course_id,
    c.name AS course_name,
    COUNT(e.id) AS enrollment_count
FROM 
    enrollment e
JOIN 
    institution i ON e.institution_id = i.id
JOIN 
    course c ON c.institution_id = i.id
JOIN 
    person p ON e.person_id = p.id
WHERE 
    e.tenant_id = :tenant_id
    AND e.institution_id = :institution_id
    AND e.is_active = TRUE  -- Considera apenas registros válidos
    AND p.metadata @> :search_term::jsonb  -- Busca por termo específico no JSONB
GROUP BY 
    c.id, c.name;

Explicação dos Filtros e Parâmetros
:tenant_id e :institution_id: Esses parâmetros são obrigatórios para filtrar o tenant e a institution.
e.is_active = TRUE: Garante que apenas registros ativos são considerados, respeitando o conceito de exclusão lógica.
p.metadata @> :search_term::jsonb: Realiza uma busca no campo metadata, filtrando por um termo específico que deve ser passado como um JSONB.
Exemplo de Parâmetro :search_term
Caso esteja buscando por registros em que metadata inclua uma chave específica como {"job_title": "professor"}, passe o parâmetro :search_term com esse valor.


----------------------
6. Construa uma consulta que retorne os alunos de um curso em uma tenant e institution específicos. Esta é uma consulta para atender a requisição que tem por objetivo alimentar uma listagem de alunos em determinado curso. Tenha em mente que poderá retornar um número grande de registros por se tratar de um curso EAD. Use boas práticas. Considere aqui também a exclusão lógica e exiba somente registros válidos.

Para construir uma consulta que retorne os alunos de um curso específico dentro de um tenant e institution definidos, levando em consideração a exclusão lógica e o potencial grande volume de registros, podemos seguir as seguintes práticas:

Filtragem por tenant_id, institution_id, e course_id: Os filtros garantem que apenas os registros necessários sejam consultados.
Exclusão lógica: Incluímos apenas registros ativos (is_active = TRUE).
Paginação: Para evitar sobrecarga de grandes conjuntos de dados em um único retorno, podemos implementar a consulta de forma paginada.
Indexação: Assegure que os campos filtrados (tenant_id, institution_id, course_id, e is_active) sejam devidamente indexados, para maximizar o desempenho da consulta.
Estrutura da Consulta:
SELECT 
    p.id AS person_id,
    p.name AS person_name,
    p.birth_date,
    p.metadata
FROM 
    enrollment e
JOIN 
    person p ON e.person_id = p.id
JOIN 
    institution i ON e.institution_id = i.id
JOIN 
    course c ON c.institution_id = i.id
WHERE 
    e.tenant_id = :tenant_id
    AND e.institution_id = :institution_id
    AND c.id = :course_id  -- Filtra pelo curso específico
    AND e.is_active = TRUE  -- Apenas registros válidos
ORDER BY 
    p.name ASC  -- Ordena por nome para exibir de forma organizada

Explicação dos Filtros e Parâmetros
:tenant_id, :institution_id, e :course_id: Filtros obrigatórios para restringir a busca ao curso específico em uma instituição e tenant definidos.
e.is_active = TRUE: Inclui apenas os registros de matrícula ativos, respeitando o conceito de exclusão lógica.
Paginação (LIMIT :limit OFFSET :offset): Limita o número de registros retornados em cada execução e permite a paginação para facilitar a navegação entre grandes conjuntos de resultados.

Exemplos de Parâmetros para Paginação
Se estiver retornando 50 registros por página:
:limit = 50
:offset = (page_number - 1) * 50
Essa estrutura ajuda a manter a consulta eficiente e escalável mesmo em cenários com um grande volume de dados, como em cursos de Educação a Distância (EAD).



-----------------------------------------------
7. Suponha que decidimos particionar a tabela enrollment. Desenvolva esta ideia. Reescreva a definição da tabela por algum critério que julgues adequado. Faça todos os ajustes necessários e comente-os.

Particionar uma tabela é uma técnica que pode melhorar o desempenho e a gerenciabilidade de grandes volumes de dados. No caso da tabela enrollment, que contém um grande volume de registros (cerca de 100.000.000), a partição pode ajudar a otimizar consultas e operações de manutenção, como backups e exclusões.

Critério de Particionamento
Para a tabela enrollment, um critério adequado de particionamento pode ser a coluna tenant_id. Isso se justifica, pois as operações geralmente são realizadas em um contexto de tenant específico, e a partição por tenant_id pode reduzir o tempo de consulta e melhorar a eficiência de operações de exclusão lógica e manutenção.

Definição da Tabela com Particionamento
A partir do PostgreSQL 10, é possível usar o particionamento nativo. Abaixo está a definição da tabela enrollment reescrita para suportar particionamento:
drop table enrollment;
CREATE TABLE enrollment (
    id SERIAL,
    tenant_id INTEGER NOT NULL,
    institution_id INTEGER,
    person_id INTEGER,
    enrollment_date DATE, 
    status VARCHAR(20),
    is_active BOOLEAN DEFAULT TRUE,  -- Coluna para exclusão lógica
    PRIMARY KEY (tenant_id, id)  -- A chave primária agora inclui tenant_id
) PARTITION BY RANGE (tenant_id);  -- Define o particionamento por tenant_id


CREATE TABLE enrollment_tenant_1 PARTITION OF enrollment
FOR VALUES FROM (1) to (100);

CREATE TABLE enrollment_tenant_2 PARTITION OF enrollment
FOR VALUES FROM (100) to (200);

-- Continue criando partições para cada tenant_id necessário


Ajustes Necessários e Comentários
Chave Primária: A chave primária agora inclui tenant_id, garantindo que cada id seja único para cada tenant, o que é crucial para a integridade dos dados em um banco de dados multi-tenant.

Particionamento por Lista: O particionamento foi feito por lista (PARTITION BY LIST), o que é ideal para cenários onde o número de tenants é conhecido e relativamente pequeno. Se o número de tenants fosse dinâmico e muito grande, uma estratégia de particionamento diferente (como por intervalo) poderia ser mais adequada.

Exclusão Lógica: A coluna is_active continua presente, permitindo a exclusão lógica. É importante ter uma abordagem clara para a exclusão lógica, especialmente em tabelas que podem ter um alto volume de operações de inserção e atualização.

Manutenção: O particionamento facilitará a manutenção do banco de dados. Por exemplo, se um tenant for removido ou se os dados de um tenant específico precisarem ser arquivados, você pode facilmente descartar a partição correspondente, o que é muito mais eficiente do que excluir registros individualmente.

Consultas: Com o particionamento, as consultas que filtram por tenant_id terão um desempenho melhor, pois o PostgreSQL pode direcionar as operações diretamente para a partição relevante, em vez de varrer toda a tabela.


Conclusão
A abordagem de particionamento para a tabela enrollment ajuda a otimizar o desempenho e a gestão de dados em um ambiente multi-tenant, ao mesmo tempo que mantém a flexibilidade necessária para lidar com operações de exclusão lógica.




Sinta-se a vontade para sugerir e aplicar qualquer ajuste que achares relevante. Comente-os