## Competency Questions

SCoRO can be used for answering several questions related to contributions in the publication process.
In the following subsections, some of them are introduced together with their respective SPARQL queries. 

The prefixes that are used in all the SPARQL queries provided below are defined as follows:

    PREFIX dcterms: <http://purl.org/dc/terms/>
    PREFIX foaf: <http://xmlns.com/foaf/0.1/>
    PREFIX pro: <http://purl.org/spar/pro/>
    PREFIX scoro: <http://purl.org/spar/scoro/>
    PREFIX tvc: <http://www.essepuntato.it/2012/04/tvc/>
    PREFIX ti: <http://www.ontologydesignpatterns.org/cp/owl/timeinterval.owl#>

### CQ1

Which role does a person hold in relation to an entity, and during what time interval?

    SELECT ?person ?entity ?role ?startDate ?endDate
    WHERE {
        ?person a foaf:Person ;
            pro:holdsRoleInTime ?roleInTime .
        ?roleInTime pro:withRole ?role ;
            scoro:relatesToEntity ?entity .
        OPTIONAL {
            ?roleInTime tvc:atTime ?timeInterval .
            OPTIONAL { ?timeInterval ti:hasIntervalStartDate ?startDate . }
            OPTIONAL { ?timeInterval ti:hasIntervalEndDate ?endDate . }
        }
    }

### CQ2

What specific contribution and level of effort did a person provide in the context of an investigation?

    SELECT ?person ?investigation ?contribution ?effort
    WHERE {
        ?person a foaf:Person ;
            pro:holdsRoleInTime ?situation .
        ?situation a scoro:ContributionSituation ;
            scoro:hasContributionContext ?investigation ;
            scoro:withContribution ?contribution .
        OPTIONAL { ?situation scoro:withEffort ?effort . }
    }

### CQ3

Which persons hold equal roles in time with respect to the same document?

    SELECT ?document ?person1 ?person2 ?role
    WHERE {
        ?person1 a foaf:Person ;
            pro:holdsRoleInTime ?roleInTime1 .
        ?person2 a foaf:Person ;
            pro:holdsRoleInTime ?roleInTime2 .
        ?roleInTime1 pro:relatesToDocument ?document ;
            pro:withRole ?role ;
            scoro:isEqualToRoleInTime ?roleInTime2 .
        FILTER(?person1 != ?person2)
    }