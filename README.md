# kafka-workshop
En workshop för Kafka

## Två olika typer av use cases
Det finns två huvudsakliga use cases i NyA till att börja med. Det första är domänevent. Domänevent är event definierade
enligt DDD (Domain Driven Development).   
Den andra typen av event är egentligen arbeten i en arbetskö. Kafka är flexibelt på så viss att man kan beroende på om
man vill parallellisera ett arbete eller om man vill ha strikt ordning på arbetena konfigurera consumer groups och 
partitioner.   

### Domänevent
Alla event skickas på något som kallas för topic. Ett topic är ungefär som en traditionell kö. Vi vill vara **väldigt** 
noga med topics. När vi talar domänevent är det **superviktigt** hur man utformar sina topics. Är det viktigt att två 
event kommer i ordningen de skapades **MÅSTE** eventen komma på samma topic och samma partition. Det är enda sättet 
Kafka garanterar ordering.   
Topics är generellt uppdelade efter domäner. För att sedan göra en finare fördelning har man ett attribut i 
JSON-strängen som kallas "typ". Denna fyller man i med vad som **HAR** hänt, till exempel "har-konverterats". Detta för 
att hjälpa consumers att sålla bort det som är ointressant för dem i en topic.

### Arbetskö
Ett jobb där ordningen av eventen som skapas inte spelar roll. Ett bra exempel är Mind. När ett dokument kommer in till 
NyA konverteras dokumentet till png:er och sedan skickas dokumentet in i NyA, samt till Mind, där Mind försöker ta reda 
på innehållet av dokumentsidorna. Här skulle man kunna starta fem instanser av Mind, om alla instancer är på samma 
consumer group kommer de att turas om att ta jobb, och därför undvika dubbelarbete.   
Det enda vi måste garantera är att dokumentet etableras i NyA innan en klassificering kommer in. Detta görs genom
att klassificeringen kommer i samma topic som "har-konverterats", nämligen "Dokument".

### Exempeluppgift

**Uppgift 1**   
Skapa en producer som producerar två olika typer av event, fast på samma topic.    
Gör sedan en consumer som enbart gör sin uppgift om ett event med en av de två typerna kommer in.

**Uppgift 2**    
Gör en producer som genererar event med siffror 1, 2, 3, etc. Skapa sedan consumers som ska ta "varannat" jobb från 
topic:et, och på så vis parallellisera jobben. 
