


-- show data interms of location , total_cases ,new_cases ,total_deaths
select location , date , total_cases , new_cases ,total_deaths , population
from dbo.Covid_Death
order by 1 ,2 ;

-- Looking for total cases vs total deaths 
select location , date , total_cases , total_deaths , (total_deaths / total_cases)* 100 as death_percent 
from dbo.Covid_Death
where location like 'india'
order by location


--Total Cases vs population 
select location , date , total_cases , population,  (total_cases / population)* 100 as total_cases_percent
from dbo.Covid_Death
where location like 'india'
order by location

-- countries with highest infection rate 

select location , max(total_cases) as max_cases , population, max((total_cases / population)) *100 as infection_rate
from dbo.Covid_Death
where location like 'india'
group by location , total_cases , population
order by infection_rate desc

-- showing countries with highest Death count per population 
select location , max(cast(total_deaths as int)) as total_death_counts
from dbo.Covid_Death
group by location
order by 2 desc


-- showing countries with highest death count by continents

select continent , max(total_deaths) as total_deaths
from dbo.Covid_Death
group by continent
order by continent desc

-- infected patients admitted to ICU

select location , total_cases , icu_patients  ,  (icu_patients / total_cases) *100 as Percent_of_ICU_Patients
from dbo.Covid_Death
where continent is not null
and icu_patients is not null 
and icu_patients != 0
group by location , total_cases , icu_patients
order by location desc


-- death percentage globally by each date 

select CONVERT(DATE, GETDATE()) date , sum(total_cases) as Total_cases,  sum(cast (total_deaths as int )) as Total_deaths, round ( sum(cast (total_deaths as int )) / sum(total_cases) , 3 ) *100 as percent_of_deaths
from dbo.Covid_Death
group by date
order by percent_of_deaths desc

-- death percentage globally 

select sum(new_cases) as Total_cases,  sum(cast (new_deaths as int )) as Total_deaths,  sum(cast (new_deaths as int )) / sum(new_cases)  *100 as percent_of_deaths
from dbo.Covid_Death
order by percent_of_deaths desc

-- Total Vaccinated by each continent

select location  , sum( cast ( people_fully_vaccinated as float)) as fully_vaccinated_people
from dbo.Covid_Vaccination
where continent is not null 
group by location

-- People fully vaccinated in total 

select death.location , death.date , death.population , vaccination.people_fully_vaccinated
from dbo.Covid_Death death join dbo.Covid_Vaccination vaccination
	on death.location = vaccination.location
	and death.date = vaccination.date

-- Total vaccinated population 

select death.location , death.date,  vaccination.new_vaccinations, 
sum(convert(float, vaccination.new_vaccinations)) over (partition by death.location order by death.location,death.date) as Rolling_value_of_vaccinated
from dbo.Covid_Death death join dbo.Covid_Vaccination vaccination
	on death.location = vaccination.location
	and death.date = vaccination.date
order by death.location


