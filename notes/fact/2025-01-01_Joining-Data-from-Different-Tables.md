---
date: 2025-01-01
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# Joining Data from Different Tables

![requirement-content-username.png](../assets/imgs/requirement-content-username.png)

For this topic, we need to fulfill the requirements above, and the results should be as shown in the table above.

![logic-query-from-database.png](../assets/imgs/logic-query-from-database.png)

According to the logic of the above diagram, we can write the SQL query as follows:

```sql
SELECT
  contents, username  -- third, filter out the columns we want to see
FROM
  comments  -- first, filter out the comments table
JOIN
  users ON comments.user_id = users.id; -- second, join the users table by user_id



--- OUTPUT ---
                                       contents                                       |      username       
--------------------------------------------------------------------------------------+---------------------
 Quo velit iusto ducimus quos a incidunt nesciunt facilis.                            | Micah.Cremin
 Non est totam.                                                                       | Frederique_Donnelly
 Fuga et iste beatae.                                                                 | Alfredo66
 Molestias tempore est.                                                               | Reyna.Marvin
 Est voluptatum voluptatem voluptatem est ullam quod quia in.                         | Reyna.Marvin
 Aut et similique porro ullam.                                                        | Reyna.Marvin
 Fugiat cupiditate consequatur sit magni at non ad omnis.                             | Reyna.Marvin
 Accusantium illo maiores et sed maiores quod natus.                                  | Micah.Cremin
 Perferendis cumque eligendi.                                                         | Reyna.Marvin
 Nihil quo voluptatem placeat.                                                        | Frederique_Donnelly
 Rerum dolor sunt sint.                                                               | Frederique_Donnelly
 Id corrupti tenetur similique reprehenderit qui sint qui nulla tenetur.              | Micah.Cremin
 Maiores quo quia.                                                                    | Reyna.Marvin
 Culpa perferendis qui perferendis eligendi officia neque ex.                         | Reyna.Marvin
 Reprehenderit voluptates rerum qui veritatis ut.                                     | Reyna.Marvin
 Aut ipsum porro deserunt maiores sit.                                                | Frederique_Donnelly
 Aut qui eum eos soluta pariatur.                                                     | Reyna.Marvin
 Praesentium tempora rerum necessitatibus aut.                                        | Gerard_Mitchell42
 Magni error voluptas veniam ipsum enim.                                              | Gerard_Mitchell42
 Et maiores libero quod aliquam sit voluptas.                                         | Micah.Cremin
 Eius ab occaecati quae eos aut enim rem.                                             | Frederique_Donnelly
 Et sit occaecati.                                                                    | Gerard_Mitchell42
 Illum omnis et excepturi totam eum omnis.                                            | Reyna.Marvin
 Nemo nihil rerum alias vel.                                                          | Frederique_Donnelly
 Voluptas ab eius.                                                                    | Frederique_Donnelly
 Dolor soluta quisquam voluptatibus delectus.                                         | Alfredo66
 Consequatur neque beatae.                                                            | Gerard_Mitchell42
 Aliquid vel voluptatem.                                                              | Gerard_Mitchell42
 Maiores nulla ea non autem.                                                          | Gerard_Mitchell42
 Enim doloremque delectus.                                                            | Reyna.Marvin
 Facere vel assumenda.                                                                | Micah.Cremin
 Fugiat dignissimos dolorum iusto fugit voluptas et.                                  | Micah.Cremin
 Sed cumque in et.                                                                    | Reyna.Marvin
 Doloribus temporibus hic eveniet temporibus corrupti et voluptatem et sint.          | Frederique_Donnelly
 Quia dolorem officia explicabo quae.                                                 | Alfredo66
 Ullam ad laborum totam veniam.                                                       | Reyna.Marvin
 Et rerum voluptas et corporis rem in hic.                                            | Micah.Cremin
 Tempora quas facere.                                                                 | Alfredo66
 Rem autem corporis earum necessitatibus dolores explicabo iste quo.                  | Frederique_Donnelly
 Animi aperiam repellendus in aut eum consequatur quos.                               | Reyna.Marvin
 Enim esse magni.                                                                     | Gerard_Mitchell42
 Saepe cumque qui pariatur.                                                           | Gerard_Mitchell42
 Sit dolorem ipsam nisi.                                                              | Gerard_Mitchell42
 Dolorem veniam nisi quidem.                                                          | Micah.Cremin
 Porro illum perferendis nemo libero voluptatibus vel.                                | Alfredo66
 Dicta enim rerum culpa a quo molestiae nam repudiandae at.                           | Micah.Cremin
 Consequatur magnam autem voluptas deserunt.                                          | Frederique_Donnelly
 Incidunt cum delectus sunt tenetur et.                                               | Gerard_Mitchell42
 Non vel eveniet sed molestiae tempora.                                               | Micah.Cremin
 Ad placeat repellat et veniam ea asperiores.                                         | Frederique_Donnelly
 Eum aut magni sint.                                                                  | Alfredo66
 Aperiam voluptates quis velit explicabo ipsam vero eum.                              | Reyna.Marvin
 Error nesciunt blanditiis quae quis et tempora velit repellat sint.                  | Micah.Cremin
 Blanditiis saepe dolorem enim eos sed ea.                                            | Reyna.Marvin
 Ab veritatis est.                                                                    | Micah.Cremin
 Vitae voluptatem voluptates vel nam.                                                 | Alfredo66
 Neque aspernatur est non ad vitae nisi ut nobis enim.                                | Gerard_Mitchell42
 Debitis ut amet.                                                                     | Gerard_Mitchell42
 Pariatur beatae nihil cum molestiae provident vel.                                   | Gerard_Mitchell42
 Aperiam sunt aliquam illum impedit.                                                  | Reyna.Marvin
 Aut laudantium necessitatibus harum eaque.                                           | Frederique_Donnelly
 Debitis voluptatum nesciunt quisquam voluptatibus fugiat nostrum sed dolore quasi.   | Alfredo66
 Praesentium velit voluptatem distinctio ut voluptatum at aut.                        | Micah.Cremin
 Voluptates nihil voluptatum quia maiores dolorum molestias occaecati.                | Reyna.Marvin
 Quisquam modi labore.                                                                | Alfredo66
 Fugit quia perferendis magni doloremque dicta officia dignissimos ut necessitatibus. | Reyna.Marvin
 Tempora ipsam aut placeat ducimus ut exercitationem quis provident.                  | Frederique_Donnelly
 Expedita ducimus cum quibusdam.                                                      | Frederique_Donnelly
 In voluptates doloribus aut ut libero possimus adipisci iste.                        | Alfredo66
 Sit qui est sed accusantium quidem id voluptatum id.                                 | Reyna.Marvin
 Libero eius quo consequatur laudantium reiciendis reiciendis aliquid nemo.           | Reyna.Marvin
 Officia qui reprehenderit ut accusamus qui voluptatum at.                            | Micah.Cremin
 Ad similique quo.                                                                    | Gerard_Mitchell42
 Commodi culpa aut nobis qui illum deserunt reiciendis.                               | Micah.Cremin
 Tenetur quam aut rerum doloribus est ipsa autem.                                     | Gerard_Mitchell42
 Est accusamus aut nisi sit aut id non natus assumenda.                               | Micah.Cremin
 Et sit et vel quos recusandae quo qui.                                               | Reyna.Marvin
 Velit nihil voluptatem et sed.                                                       | Gerard_Mitchell42
 Sunt vitae expedita fugiat occaecati.                                                | Reyna.Marvin
 Consequatur quod et ipsam in dolorem.                                                | Gerard_Mitchell42
 Magnam voluptatum molestias vitae voluptatibus beatae nostrum sunt.                  | Alfredo66
 Alias praesentium ut voluptatem alias praesentium tempora voluptas debitis.          | Micah.Cremin
 Ipsam cumque aut consectetur mollitia vel quod voluptates provident suscipit.        | Alfredo66
 Ad dignissimos quia aut commodi vel ut nisi.                                         | Alfredo66
 Fugit ut architecto doloremque neque quis.                                           | Gerard_Mitchell42
 Repudiandae et voluptas aut in excepturi.                                            | Frederique_Donnelly
 Aperiam voluptatem animi.                                                            | Frederique_Donnelly
 Et mollitia vel soluta fugiat.                                                       | Gerard_Mitchell42
 Ut nemo voluptas voluptatem voluptas.                                                | Frederique_Donnelly
 At aut quidem voluptatibus rem.                                                      | Frederique_Donnelly
 Temporibus voluptates iure fuga alias minus eius.                                    | Micah.Cremin
 Non autem laboriosam consectetur officiis aut excepturi nobis commodi.               | Gerard_Mitchell42
 Esse voluptatem sed deserunt ipsum eaque maxime rerum qui.                           | Frederique_Donnelly
 Debitis ipsam ut pariatur molestiae ut qui aut reiciendis.                           | Gerard_Mitchell42
 Illo atque nihil et quod consequatur neque pariatur delectus.                        | Alfredo66
 Qui et hic accusantium odio quis necessitatibus et magni.                            | Gerard_Mitchell42
 Debitis repellendus inventore omnis est facere aliquam.                              | Alfredo66
 Occaecati eos possimus deleniti itaque aliquam accusamus.                            | Alfredo66
 Molestiae officia architecto eius nesciunt.                                          | Frederique_Donnelly
 Minima dolorem reiciendis excepturi culpa sapiente eos deserunt ut.                  | Alfredo66
(100 rows)
```

You can also imagine the second step as merging two tables into one imaginary table as follows, for easier understanding:

![join-two-table-to-one.png](../assets/imgs/join-two-table-to-one.png)


