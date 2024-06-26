---
header-id: generating-staged-models-using-service-builder
---

# Generating Staged Models Using Service Builder

[TOC levels=1-4]

<aside class="alert alert-info">
    <span class="wysiwyg-color-blue120">This document has been updated and ported to <a href="https://learn.liferay.com/web/guest/w/dxp/building-applications/data-frameworks/service-builder/service-builder-basics/generating-model-persistence-and-service-code">Liferay Learn</a> and is no longer maintained here.</span>
</aside>

Instead of having to create staged models for your app manually, you can
leverage
[Service Builder](/docs/7-2/appdev/-/knowledge_base/a/service-builder) to
generate the necessary staged model logic for you. If your app doesn't
use Liferay's Service Builder, you must configure it in your project. This
<!--
Add link back for 'configure it in your project' once
defining-an-object-relational-map-with-service-builder article is available
-->
tutorial assumes you have a Service Builder project with `*api` and `*service`
modules. If you want to follow along with this tutorial, download the
staged-model-example
Service Builder project (Gradle-based).

<!-- TODO: Update the example above when we can upload projects to new site.-->

You'll track the Service Builder-generated changes applied to an entity model
file to observe how staged models are assigned to your entity. Keep in mind the
specific
[staged attributes](/docs/7-2/frameworks/-/knowledge_base/f/developing-staged-models#staged-model-attributes)
necessary for each staged model. Depending on the attributes defined in your
`service.xml` file, Service Builder assigns your entity model to a specific
staged model type.

1.  Navigate to your project's `*service` module at the command line. Run
    Service Builder (e.g., `gradlew buildService`) to generate your project's
    models based on the current `service.xml` configuration.

2.  Open your project's `[Entity]Model.java` interface and observe the inherited
    interfaces.

    ```java
    public interface FooModel extends BaseModel<Foo>, ShardedModel, StagedModel {
    ```

    Your model was generated as a staged model! This is because the
    `service.xml` file sets the UUID to `true` and defines the `companyId`,
    `createDate`, and `modifiedDate` columns. There is much more logic generated
    for your app behind the scenes, but this shows that Service Builder deemed
    your entity eligible for the Staging and Export/Import frameworks.

3.  Add the `userId` and `userName` columns to your `service.xml` file:

    ```xml
    <column name="userId" type="long" />
    <column name="userName" type="String" />
    ```

4.  Rerun Service Builder and observe your `[Entity]Model.java` interface again:

    ```java
    public interface FooModel extends BaseModel<Foo>, GroupedModel, ShardedModel,
        StagedAuditedModel {
    ```

    Your model is now a staged audited model!

5.  Add the `lastPublishDate` column to your `service.xml` file:

    ```xml
    <column name="lastPublishDate" type="Date" />
    ```

6.  Rerun Service Builder and observe your `[Entity]Model.java` interface again:

    ```java
    public interface FooModel extends BaseModel<Foo>, ShardedModel,
        StagedGroupedModel {
    ```

    Your model is now a staged grouped model! The `groupId` column is also
    required to extend the `StagedGroupedModel` interface, but it was already
    defined in the original `service.xml` file.

Fantastic! You've witnessed firsthand how easy it is to generate staged models
using Service Builder.