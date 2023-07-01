# Contexto

O que é um **ORM**? O orm vai fazer com que o **O**bjeto(Model) represente os dados de um banco de dados **R**elacional **M**apeando o mesmo para um objeto.
Cada tabela representamos por um classe que extende o [AbstractModel](https://github.com/magento/magento2/blob/2.4.6/lib/internal/Magento/Framework/Model/AbstractModel.php).

# Mão na massa!

## Estrutura de arquivos
Pra podermos colocar a mão na massa vamos ter que entender como funcionam a estrutura de pastas do magento.
![file structure](https://i.ibb.co/gy6vPCm/print1tab.png "File Structure")
## Criando uma vendor
Para esse post precisamos apenas da pasta app, ali criamos a nossa Vendor para a criação dos modulos.
Vou chamar ela de [TabNews](https://www.tabnews.com.br/)
![Criando uma vendor](https://i.imgur.com/8bwgm7k.png "Criando uma vendor")

## Criando um modulo
Dentro dele vamos criar nosso modulo, vou chama-lo de BlogExample.
![Criando o modulo](https://i.imgur.com/QYAIQof.png "Criando o modulo")
Dentro de um modulo temos alguns arquivos que são obrigatorios:
### Arquivos Obrigatorios

#### registration.php

Repare na estrutura onde chamamos o *ComponentRegistrar* para registrar nosso modulo e vou chamar a atenção para o segundo paramentro de que passamos 

```php
<?php

use Magento\Framework\Component\ComponentRegistrar;

ComponentRegistrar::register(ComponentRegistrar::MODULE, 'TabNews_BlogExample', __DIR__);
```

#### etc/module.php

Aqui registramos o modulo e sua dependencias:

```xml
<?xml version="1.0" ?>
<config xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="urn:magento:framework:Module/etc/module.xsd">
    <module name="TabNews_BlogExample">
        <sequence>
            <module name="Magento_Store"/>
            <module name="Magento_Theme"/>
            <module name="Magento_Variable"/>
        </sequence>
    </module>
</config>
```
Como no nosso modulo n temos dependencias de um modulo em especifico fica assim:

```xml
<?xml version="1.0"?>
<config xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xsi:noNamespaceSchemaLocation="urn:magento:framework:Module/etc/module.xsd">
    <module name="TabNews_BlogExample"/>
</config>

```
É importante ressaltar a nomeclatura vendor_module, ela é assim que o magento referencia o modulo como vamos ver no proximo item.
 
## Listando o modulo

Na linha de comando vamos usar o `bin/magento module:status`.

Nele vamos obter uma lista de todos os modulos e tambem podemos ver quais estão ativos  quais não estão como é o caso do nosso modulo:
![modulo desativado](https://i.imgur.com/v4C50Na.png)

## Ativando o modulo

Novamente vamos para a linha de comando para ativa-lo vamos rodar `bin/magento module:enable TabNews_BlogExample`.
![ativando modulo](https://i.imgur.com/xWlTu8f.png).

Note que ele pede pra fazermos outros passos que vou explicar mais pra frente, como nosso modulo esta vazio não tem a necessidade de rodar esses comandos

## Criando nossa tabela no banco

Para isso precisamos criar nosso `db_schema.xml` mas antes vamos entender o que ele faz e os campos para preenchermos, vamos pegar de exemplo um arquivo da tabela de customers do magento, no caso vou pegar apenas a tabela da entidade se quiser ver o [arquivo completo](https://github.com/magento/magento2/blob/2.4.6/app/code/Magento/Customer/etc/db_schema.xml)

```xml
<?xml version="1.0"?>
<schema xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xsi:noNamespaceSchemaLocation="urn:magento:framework:Setup/Declaration/Schema/etc/schema.xsd">
    <table name="customer_entity" resource="default" engine="innodb" comment="Customer Entity">
        <column xsi:type="int" name="entity_id" unsigned="true" nullable="false" identity="true"
                comment="Entity ID"/>
        <column xsi:type="smallint" name="website_id" unsigned="true" nullable="true" identity="false"
                comment="Website ID"/>
        <column xsi:type="varchar" name="email" nullable="true" length="255" comment="Email"/>
        <column xsi:type="smallint" name="group_id" unsigned="true" nullable="false" identity="false"
                default="0" comment="Group ID"/>
        <column xsi:type="varchar" name="increment_id" nullable="true" length="50" comment="Increment ID"/>
        <column xsi:type="smallint" name="store_id" unsigned="true" nullable="true" identity="false"
                default="0" comment="Store ID"/>
        <column xsi:type="timestamp" name="created_at" on_update="false" nullable="false" default="CURRENT_TIMESTAMP"
                comment="Created At"/>
        <column xsi:type="timestamp" name="updated_at" on_update="true" nullable="false" default="CURRENT_TIMESTAMP"
                comment="Updated At"/>
        <column xsi:type="smallint" name="is_active" unsigned="true" nullable="false" identity="false"
                default="1" comment="Is Active"/>
        <column xsi:type="smallint" name="disable_auto_group_change" unsigned="true" nullable="false"
                identity="false" default="0" comment="Disable automatic group change based on VAT ID"/>
        <column xsi:type="varchar" name="created_in" nullable="true" length="255" comment="Created From"/>
        <column xsi:type="varchar" name="prefix" nullable="true" length="40" comment="Name Prefix"/>
        <column xsi:type="varchar" name="firstname" nullable="true" length="255" comment="First Name"/>
        <column xsi:type="varchar" name="middlename" nullable="true" length="255" comment="Middle Name/Initial"/>
        <column xsi:type="varchar" name="lastname" nullable="true" length="255" comment="Last Name"/>
        <column xsi:type="varchar" name="suffix" nullable="true" length="40" comment="Name Suffix"/>
        <column xsi:type="date" name="dob" comment="Date of Birth"/>
        <column xsi:type="varchar" name="password_hash" nullable="true" length="128" comment="Password_hash"/>
        <column xsi:type="varchar" name="rp_token" nullable="true" length="128" comment="Reset password token"/>
        <column xsi:type="datetime" name="rp_token_created_at" on_update="false" nullable="true"
                comment="Reset password token creation time"/>
        <column xsi:type="int" name="default_billing" unsigned="true" nullable="true" identity="false"
                comment="Default Billing Address"/>
        <column xsi:type="int" name="default_shipping" unsigned="true" nullable="true" identity="false"
                comment="Default Shipping Address"/>
        <column xsi:type="varchar" name="taxvat" nullable="true" length="50" comment="Tax/VAT Number"/>
        <column xsi:type="varchar" name="confirmation" nullable="true" length="64" comment="Is Confirmed"/>
        <column xsi:type="smallint" name="gender" unsigned="true" nullable="true" identity="false"
                comment="Gender"/>
        <column xsi:type="smallint" name="failures_num" unsigned="false" nullable="true" identity="false"
                default="0" comment="Failure Number"/>
        <column xsi:type="timestamp" name="first_failure" on_update="false" nullable="true" comment="First Failure"/>
        <column xsi:type="timestamp" name="lock_expires" on_update="false" nullable="true"
                comment="Lock Expiration Date"/>
        <column xsi:type="timestamp" name="session_cutoff" on_update="false" nullable="true"
                comment="Session Cutoff Time"/>
        <constraint xsi:type="primary" referenceId="PRIMARY">
            <column name="entity_id"/>
        </constraint>
        <constraint xsi:type="foreign" referenceId="CUSTOMER_ENTITY_STORE_ID_STORE_STORE_ID" table="customer_entity"
                    column="store_id" referenceTable="store" referenceColumn="store_id" onDelete="SET NULL"/>
        <constraint xsi:type="foreign" referenceId="CUSTOMER_ENTITY_WEBSITE_ID_STORE_WEBSITE_WEBSITE_ID"
                    table="customer_entity" column="website_id" referenceTable="store_website"
                    referenceColumn="website_id" onDelete="SET NULL"/>
        <constraint xsi:type="unique" referenceId="CUSTOMER_ENTITY_EMAIL_WEBSITE_ID">
            <column name="email"/>
            <column name="website_id"/>
        </constraint>
        <index referenceId="CUSTOMER_ENTITY_STORE_ID" indexType="btree">
            <column name="store_id"/>
        </index>
        <index referenceId="CUSTOMER_ENTITY_WEBSITE_ID" indexType="btree">
            <column name="website_id"/>
        </index>
        <index referenceId="CUSTOMER_ENTITY_FIRSTNAME" indexType="btree">
            <column name="firstname"/>
        </index>
        <index referenceId="CUSTOMER_ENTITY_LASTNAME" indexType="btree">
            <column name="lastname"/>
        </index>
    </table>
</schema>
```
Vou dividir o arquivo xml pelos seus nós para explicar melhor:

### `<table/>`

#### `name`

 Aqui vamos colocar o nome da nossa tabela
 
#### `resource`

 Essa opção é caso você use o banco fragmentado(aparentemente não disponivel na versão community), nele podemos passar 3 opções conforme podemos ver no [xsd do xml](https://github.com/magento/magento2/blob/2.4.6/lib/internal/Magento/Framework/Setup/Declaration/Schema/etc/schema.xsd#L99L105):
 
```xml
    <xs:simpleType name="resourceType">
        <xs:restriction base="xs:string">
            <xs:enumeration value="default" />
            <xs:enumeration value="checkout" />
            <xs:enumeration value="sales" />
        </xs:restriction>
    </xs:simpleType>
```

##### `default`
Nesse caso ele guardaria a tabela junto com as demais, sem separação das demais.

##### `checkout`
Salvaria nossa tabela na database separada do checkout.

##### `sales`
Salvaria nossa tabela na database separada do sales.

#### `comment`
Comentario na tabela

#### `engine`

Aqui definimos a forma com que ela vai ser salva na base temos 2 valores possiveis aqui conforme o [xsd](https://github.com/magento/magento2/blob/2.4.6/lib/internal/Magento/Framework/Setup/Declaration/Schema/etc/schema.xsd#L107L112):

##### `innodb`

Forma padrão cria uma tabela no banco como conhecemos.

##### `memory`

Forma padrão cria uma tabela porem ela é salva na memoria, tome cuidado com esse tipo de engine, guarde apenas dados temporarios, tendo em vista que vão deixar de existir apos um reboot ou similar.

### `<column/>`

Aqui vamos declarar as colunas da tabela:


```xml
<?xml version="1.0"?>
<schema xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="urn:magento:framework:Setup/Declaration/Schema/etc/schema.xsd">
    <table name="customer_entity" resource="default" engine="innodb" comment="Customer Entity">
        <column xsi:type="int" name="entity_id" unsigned="true" nullable="false" identity="true"comment="Entity ID"/>
    </table>
</schema>
```

#### `types`
 
 Aqui vão ser os tipos de campos disponiveis:
 
- boolean
- float
- decimal
- double
- integer
- smallinteger
- tinyinteger
- biginteger
- longtext
- char
- text
- varchar
- json
- mediumtext
- mediumblob
- varbinary
- longblob
- blob
- column
- datetime
- date
- timestamp
- default

#### `name`

Aqui colocamos o nome da nossa coluna

#### `unsigned`

Para dados numericos ele vai representar se o campo pode ou não ter um dado negativo

#### `nullable`

Especifica se o valor pode ser nulo

#### `identity`

Especifica se a coluna é incremetada automaticamente
