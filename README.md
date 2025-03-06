# se2-einzelprojekt-service

This project contains a Spring Boot web service written in Kotlin and uses Gradle as build system.

The web service consists of two REST controllers managing game results and a leaderboard.
Access the service's OpenAPI description at http://localhost:8080/swagger-ui.html.

____
# 2.1 Einlesen ins Projekt
„Clonen“ Sie das GitHub Repository mit git auf Ihr Gerät und machen Sie sich mit Kotlin’s
Syntax und der Architektur des Projekts vertraut.

``` bash
git clone https://github.com/AAU-SE2/se2-einzelprojekt-service.git
```

## Architektur des Projekts
![image](https://github.com/user-attachments/assets/8dd8afaa-0214-43d7-ac1c-889fbe5c7aad)


#### Controllers
Ein Controller empfängt HTTP Requests, gibt eine Antwort im JSON-Format und definiert die REST-Endpunkte.

**GameResultController:**
Empfängt HTTP-Requests von GameResult-Entitäten. 

``` Kotlin
// Basis URL für alle Endpoints in diesem Controller
@RequestMapping("\game-results") 

@RequestBody... // mappt JSON zu einem GameResult Objekt

@PathVariable ... // bindet eine URL-Variable an eine Methodenvariable.
// z.B. /{gameResultId} zu gameResultId: Long


// Endpoints:

// 1: GET /game-results/{gameResultId}
// Gibt ein Game aufgrund der Game-ID zurück

// 2: GET /game-results
// Gibt alle Games als Liste zurück

// 3: POST /game-results
// fügt ein Game-Result zur Liste hinzu

// 4: DELETE /game-results/{gameResultId}
// löscht ein Game nach der Game-ID
```

**LeaderboardController:**
Hier gibt es nur einen Endpunkt, nämlich getLeaderboard(), welches eine das Leaderboard als Rangliste zurückgibt. Die Liste wird absteigend nach dem Score und aufsteigend nach der ID gereiht.

#### Models
**GameResult:**
Ist ein Data-Klasse in Kotlin, die dazu da ist, Daten zu speichern. Es werden einige hilfreiche Methoden automatisch generiert.

#### Services
**GameResultService:**
Logik, um GameResultObjects zu managen. Stellt eine In-Memory-Liste zur Verfügung. Sie stellt Methoden bereit, um:
- GameResults zur Liste hinzuzufügen
- GameResults von der Liste zu entfernen
- Alle GameResults zurückzugeben
- Ein GameResult aus der Liste zu entfernen

___
### Vorgehensweise:
SpringApp starten und danach mit Link unten auf Webservice zugreifen:

[Link zum Server](http://localhost:8080/swagger-ui.html)

___
# 2.2 Ändern der Implementierung
- Leaderboard vor der Änderung:
	- Es wird bei **gleichem Score** nach der **Id** aufsteigend sortiert
![image](https://github.com/user-attachments/assets/226658d9-d6d0-4574-87ec-cca37f1f6d2a)

- Die Tests sind erfolgreich
![image](https://github.com/user-attachments/assets/751220c0-4d38-4084-bf5a-9833d6b6745a)

___

- Nach meiner Änderung wird bei **gleichem Score** nach der **timeInSeconds** absteigend sortiert
``` Kotlin
@GetMapping  
fun getLeaderboard(): List<GameResult> =  
    gameResultService.getGameResults().sortedWith(compareBy({ -it.score }, { it.timeInSeconds }))
```
![image](https://github.com/user-attachments/assets/95d712f3-001c-4393-a51f-c6702578d5be)

- Nun schlägt der Test aber fehl

- Dieser muss angepasst werden:
```Kotlin
@Test  
fun test_getLeaderboard_sameScore_CorrectTimeSorting() {  
    val first = GameResult(1, "first", 20, 20.0)  
    val second = GameResult(2, "second", 20, 10.0)  
    val third = GameResult(3, "third", 20, 15.0)  
  
    whenever(mockedService.getGameResults()).thenReturn(listOf(second, third, first))  
  
    val res: List<GameResult> = controller.getLeaderboard()  
  
    verify(mockedService).getGameResults()  
    assertEquals(3, res.size)  
    assertEquals(second, res[0])  
    assertEquals(third, res[1])  
    assertEquals(first, res[2])  
}
```

- Nun ist der Test erfolgreich
  
- Und das Leaderboard wird auch richtig ausgegeben:
![image](https://github.com/user-attachments/assets/721d05d3-973b-4a4d-ac6c-03140ffe66da)

___
Nach dem commit habe ich das Projekt auf mein Github-Repository gepusht ([Link](https://github.com/hoffmann2109/Software_Engineering_II_Uni)):

```bash
thomas@fedora ~> git remote remove origin
thomas@fedora ~> git remote -v
thomas@fedora ~> git remote add origin git@github.com:hoffmann2109/Software_Engineering_II_Uni.git
thomas@fedora ~> git branch -M main
thomas@fedora ~> git push -u origin main
```

![image](https://github.com/user-attachments/assets/a64cccfa-bdde-4c1f-aca9-b89b601cab7f)


- Alle Tests waren erfolgreich ([Link](https://github.com/hoffmann2109/Software_Engineering_II_Uni/actions/runs/13648251669/job/38151005043)):

# 3. Abgabe
Anschließend habe ich die XML-Datei befüllt:
``` XML
<person>  
    <name>Thomas Hoffmann</name>  
    <matrikelnummer>xxxxxxxx</matrikelnummer>  
    <lastcommithash>xxxxxxxx</lastcommithash>  
    <githubusername>hoffmann2109</githubusername>  
    <repositoryname>Software_Engineering_II_Uni</repositoryname>  
    <repositoryurl>https://github.com/hoffmann2109/Software_Engineering_II_Uni</repositoryurl>  
</person>
```

Und den Test ausgeführt.

