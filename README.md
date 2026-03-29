# R-Project
# Complete R-script

#Install required packages if missing --
packages <- c(
  "cowplot",
  "googleway",
  "ggplot2",
  "ggrepel",
  "ggspatial",
  "libwgeom",
  "sf",
  "rnaturalearth",
  "rnaturalearthdata",
  "rvest",
  "dplyr",
  "viridis",
  "ggthemes",
  "plm",
  "geometry",
  "tidyverse",
  "haven",
  "viridisLite"
)

missing_packages <- packages[!(packages %in% installed.packages()[,"Package"])]
if(length(missing_packages) > 0){
  install.packages(missing_packages)
}

# Load libraries --
library(ggplot2)
library(sf)
library(rvest)
library(dplyr)
library(viridis)
library(ggrepel)
library(ggthemes)
library(plm)
library(geometry)
library(tidyverse)
library(haven)
library(rnaturalearth)
library(rnaturalearthdata)
library(viridisLite)

theme_set(theme_bw())




#Set your directory and download the dataset(final_data)

final_data <- read_dta("C:/Users/Downloads/final_data.dta")


final_data$Round <- as.numeric(as.character(final_data$Round))
final_data$post <- ifelse(final_data$Round>=4,1,0)
final_data$treated <- ifelse(final_data$HUMDelivered == "1", 1, 0)


# Make sure the data is sorted by 'SettlementCode' and 'Round' since our data is panel 
final_data <- final_data %>% arrange(SettlementCode, Round)

# Create a variable representing the change in 'ArrivalIDPs' for each panel
final_data <- final_data %>%
  group_by(SettlementCode) %>%
  mutate(Change_ArrivalIDPs = c(NA, diff(ArrivalIDPs)))


final_data$SecSituation_factor <- as.factor(final_data$SecSituation)

# Simple OLS
Ols1 <- lm(ArrivalIDPs ~ treated, data = final_data)
summary(Ols1)


# For our DiD model, we need the interaction between treatment and post
final_data$did <- final_data$treated * final_data$post

#################################
#ArrivalIDPs
#################################

# To estimate a DiD:
did_simple1 <- lm(ArrivalIDPs ~ treated + post + did, data = final_data)
summary(did_simple1)  

# Adding controls:
did_simple_with_x <- lm(ArrivalIDPs ~ treated + post + did + HLTClinics + EduSchoolExist + SecSituation_factor, data = final_data)
summary(did_simple_with_x)  



# Province Fixed-effect:
final_data$Province_factor <- as.factor(final_data$Province)
did_Province <- lm(ArrivalIDPs ~ post + did + Province_factor, data = final_data)
summary(did_Province) 

# Time Fixed-effect:
final_data$Round_factor <- as.factor(final_data$Round)
did_Round <- lm(ArrivalIDPs ~ post + did + Round_factor, data = final_data)
summary(did_Round) 


# Model with two-way FE
# post" should now also be dropped because the post-treatment period is implied by the year FE
did_Province_Round <- lm(ArrivalIDPs ~ Round_factor + did + Province_factor, data = final_data)
summary(did_Province_Round) 



#Plot Trend
library(ggplot2)
final_data$treated <- as.factor(final_data$treated)
ggplot(final_data, aes(x=Round, y=ArrivalIDPs, col=treated)) + 
  coord_cartesian(xlim = c(1,6),ylim = c(0,1000)) +
  xlab("Round") + ylab("ArrivalIDPs") +
  scale_x_continuous(breaks = seq(min(final_data$Round), max(final_data$Round), by = 1)) +
  scale_y_continuous(breaks = (seq(0, 1000, by = 100))) +
  stat_summary(fun = mean, geom = 'line') +
  geom_vline(xintercept = 4) + 
  ggtitle("Evolution of outcome over time")

final_data$HLTClinics <- factor(final_data$HLTClinics, labels = c("control", "treated"))
ggplot(final_data, aes(x=Round, y=ArrivalIDPs, col=HLTClinics)) + 
  coord_cartesian(xlim = c(1,6),ylim = c(0,1000)) +
  xlab("Round") + ylab("IDP Arrivals") +
  scale_x_continuous(breaks = seq(1, 6, by = 1)) +
  scale_y_continuous(breaks = seq(0, 1000, by = 100)) +
  stat_summary(fun = mean, geom = 'line') +
  geom_vline(xintercept = 4) + 
  ggtitle("IDPs Arrival over different rounds")


final_data$EduSchoolExist <- factor(final_data$EduSchoolExist, labels = c("control", "treated"))
ggplot(final_data, aes(x=Round, y=ArrivalIDPs, col=EduSchoolExist)) + 
  coord_cartesian(xlim = c(1,6),ylim = c(0,1000)) +
  xlab("Round") + ylab("IDP Arrivals") +
  scale_x_continuous(breaks = seq(1, 6, by = 1)) +
  scale_y_continuous(breaks = seq(0, 1000, by = 100)) +
  stat_summary(fun = mean, geom = 'line') +
  geom_vline(xintercept = 4) + 
  ggtitle("IDPs Arrival over different rounds")


################################
#Chnage in Arrival IDPs
################################

# Simple OLS
Ols2 <- lm(Change_ArrivalIDPs ~ treated, data = final_data)
summary(Ols2)


# To estimate a DiD:
did_simple2 <- lm(Change_ArrivalIDPs ~ treated + post + did, data = final_data)
summary(did_simple2)  

# Adding controls:
did_simple_with_x2 <- lm(Change_ArrivalIDPs ~ treated + post + did + HLTClinics + EduSchoolExist + SecSituation_factor, data = final_data)
summary(did_simple_with_x2)  



# Province Fixed-effect:
did_Province2 <- lm(Change_ArrivalIDPs ~ post + did + Province_factor, data = final_data)
summary(did_Province2) 

# Time Fixed-effect:
did_Round2 <- lm(Change_ArrivalIDPs ~ post + did + Round_factor, data = final_data)
summary(did_Round2) 


# Model with two-way FE
# post" should now also be dropped because the post-treatment period is implied by the year FE
did_Province_Round2 <- lm(Change_ArrivalIDPs ~ Round_factor + did + Province_factor, data = final_data)
summary(did_Province_Round2) 



#Plot Trend
library(ggplot2)
final_data$treated <- as.factor(final_data$treated)
ggplot(final_data, aes(x=Round, y=Change_ArrivalIDPs, col=treated)) + 
  coord_cartesian(xlim = c(1,6),ylim = c(-150,100)) +
  xlab("Round") + ylab("ChangeInArrivalIDPs") +
  scale_x_continuous(breaks = seq(min(final_data$Round), max(final_data$Round), by = 1)) +
  scale_y_continuous(breaks = (seq(-150, 100, by = 20))) +
  stat_summary(fun = mean, geom = 'line') +
  geom_vline(xintercept = 4) + 
  ggtitle("Evolution of outcome over time")


final_data$HLTClinics <- factor(final_data$HLTClinics, labels = c("control", "treated"))
ggplot(final_data, aes(x=Round, y=Change_ArrivalIDPs, col=HLTClinics)) + 
  coord_cartesian(xlim = c(1,6),ylim = c(-70,100)) +
  xlab("Round") + ylab("Change in IDP Arrivals") +
  scale_x_continuous(breaks = seq(1, 6, by = 1)) +
  scale_y_continuous(breaks = seq(-70, 100, by = 10)) +
  stat_summary(fun = mean, geom = 'line') +
  geom_vline(xintercept = 4) + 
  ggtitle("Change in IDPs Arrival over different rounds")


final_data$EduSchoolExist <- factor(final_data$EduSchoolExist, labels = c("control", "treated"))
ggplot(final_data, aes(x=Round, y=Change_ArrivalIDPs, col=EduSchoolExist)) + 
  coord_cartesian(xlim = c(1,6),ylim = c(-70,100)) +
  xlab("Round") + ylab("Chnage in IDP Arrivals") +
  scale_x_continuous(breaks = seq(1, 6, by = 1)) +
  scale_y_continuous(breaks = seq(-70, 100, by = 10)) +
  stat_summary(fun = mean, geom = 'line') +
  geom_vline(xintercept = 4) + 
  ggtitle("Change in IDPs Arrival over different rounds")

################################
#Maping
################################

shp0 <-  read_sf("C:/Users/Downloads/AFG_adm/AFG_adm0.shp")

shp1 <-  read_sf("C:/Users/Downloads/AFG_adm/AFG_adm1.shp")

shp2 <-  read_sf("C:/Users/Downloads/AFG_adm/AFG_adm2.shp")


shp1 <- shp1 %>%
  rename(Province = NAME_1)


#Names changes since I have slightly different provinces names in the IOM's Dataset.
shp1$Province[shp1$Province == "Hirat"] <- "Herat"
shp1$Province[shp1$Province == "Hilmand"] <- "Helmand"
shp1$Province[shp1$Province == "Panjshir"] <- "Panjsher"
shp1$Province[shp1$Province == "Sari Pul"] <- "Sar-e-Pul"


mapdata1<-merge(final_data, shp1, by="Province")


province_round_data <- aggregate(Change_ArrivalIDPs ~ Province + Round, data = mapdata1, sum)
merged_data1 <- merge(province_round_data, shp1[, c("Province", "geometry")], by = "Province", all.x = TRUE)


#Change in Arrival IDPs in provinces 
ggplot() + 
  geom_sf(data = merged_data1, aes(fill = Change_ArrivalIDPs, geometry = geometry), color = "black", size = 0.3) +
  scale_fill_gradientn(name = "",
                       colours = c("lightgreen", "whitesmoke", "red"),
                       values = scales::rescale(c(min(merged_data1$Change_ArrivalIDPs), 0, max(merged_data1$Change_ArrivalIDPs))),
                       labels = scales::label_number(accuracy = 1)) +
  labs(title = "Change in Arrival IDPs by Province",
       subtitle = "Map",
       caption = "Source: International Organization for Migration") +
  theme_bw() +
  theme(
    axis.text = element_blank(),
    axis.ticks = element_blank(),
  ) +
  facet_wrap(~Round)


# Number of arrival IDPs by provinces
province_round_data2 <- aggregate(ArrivalIDPs ~ Province + Round, data = mapdata1, sum)
merged_data2 <- merge(province_round_data2, shp1[, c("Province", "geometry")], by = "Province", all.x = TRUE)



ggplot(merged_data2, aes(fill = ArrivalIDPs, geometry = geometry)) +
  geom_sf(color = "white", size = 0.3) +
  scale_fill_gradientn(name = "",
                       colours = c("lightgreen", "whitesmoke", "red"),
                       values = scales::rescale(c(min(merged_data2$ArrivalIDPs), 0, max(merged_data2$ArrivalIDPs))),
                       labels = scales::label_number(accuracy = 1)) +
  labs(title = "Number of Arrival IDPs by Province",
       subtitle = "Map",
       caption = "Source: International Organization for Migration") +
  theme_bw() +
  theme(
    axis.text = element_blank(),
    axis.ticks = element_blank(),
  ) +
  facet_wrap(~Round)


#
#To map every settlement included in the dataset on a map:

settelements_sf <- st_as_sf(final_data, coords = c("Longitude", "Latitude"), crs = 4326)
st_crs(final_data)$epsg

final_data$Latitude <- as.numeric(final_data$Latitude)
final_data$Longitude <- as.numeric(final_data$Longitude)
final_data <- final_data[complete.cases(final_data$Longitude, final_data$Latitude), ]



plot(st_geometry(settelements_sf), axes = TRUE, pch = 20, col = "black", main = "Settlements", cex = 0.5)

# plot districts, Provinces and settlements
pl3 <- ggplot() +
  geom_sf(data = shp2, aes(fill = NAME_1)) +
  geom_sf(data = settelements_sf, color = "black", shape = 20, size = 1) +
  ggthemes::theme_map() +
  theme(legend.position = "none") +
  labs(title = "Afghanistan Provinces map with Settlements")

print(pl3)


#or
#Assuming we have loaded the districts_sf and settelements_sf objects

pl3 <- ggplot() +
  geom_sf(data = shp2, fill = "white", color = "black") +
  geom_sf(data = settelements_sf, color = "blue", shape = 20, size = 1) +
  theme(legend.position = "none") +
  labs(title = "Afghanistan Provinces map with Settlements")

print(pl3)




#################################################
#plot settlements that have received humanitarian aids and those that did not:
##################################################

########  Maping Security-Situation  ######

# Create a list of colors corresponding to SecSituation values
sec_colors <- c("darkred", "red", "orange", "yellow", "lightgreen", "green")

# Mapping numeric rounds to corresponding dates
settelements_sf$RoundDate <- factor(settelements_sf$Round, 
                                    levels = 1:6,
                                    labels = c("January 2020", "December 2020", "March 2021", 
                                               "June 2021", "December 2021", "December 2022"))

# Define custom levels and labels for SecSituation
sec_levels <- c(0, 1, 2, 3, 4, 5)
sec_labels <- c("Not Applicable", "Very Insecure", "Insecure", "Neutral", "Secure", "Very Secure")

pl_sec <- ggplot() +
  geom_sf(data = shp2, fill = "white", color = "black") +
  geom_sf(data = settelements_sf, aes(color = factor(SecSituation, levels = sec_levels)), shape = 20, size = 0.5) +
  scale_color_manual(values = sec_colors, 
                     labels = sec_labels) +
  theme(legend.position = "bottom") +
  labs(title = "Settlements Map by Security Situation") +
  facet_wrap(~RoundDate) +
  guides(color = guide_legend(override.aes = list(size = 4)))  # Adjust the 'size' parameter as needed

print(pl_sec)


###############################################################################################################
#Districts
###############################################################################################################

# Convert the dataframe to an sf object

District_Map <- read_sf("C:/Users/Downloads/GIS/Afghanistan_Districts.shp")
shp_D <-  read_sf("C:/Users/Downloads/Final_Shp/Final-point.shp")


# Assuming DIST_34_NA is the column you want to merge on
shp1 <- st_join(District_Map, shp_D)


shp1 <- shp1[, c("DIST_34_NA.y", "geometry", "DISTID.x", "PROVID.x")]


# Plot the polygons
plot(shp1)


shp1 <- shp1 %>%
  rename(District = DIST_34_NA.y)

mapdata_D_1<-merge(final_data, shp1, by="District")


#new
District_round_data <- aggregate(Change_ArrivalIDPs ~ District + Round + HUMDelivered, data = mapdata_D_1, sum)
merged_data_D1 <- merge(shp1, District_round_data [, c("District", "Round","Change_ArrivalIDPs","HUMDelivered")], by = "District", all.x = TRUE)


# Create a new variable indicating the category
merged_data_D_11 <- merged_data_D1 %>%
  mutate(Change_ArrivalIDPs_category = cut(Change_ArrivalIDPs,
                                           breaks = c( -Inf, 0, 1000, 2000, 3000, 4000, 5000, 6000, 10000, Inf),
                                           labels = c("-Ve", "01-1000", "1001-2000", "2001-3000", "3001-4000", "4001-5000", "5001-6000", "6001-10000", "10000+"),
                                           include.lowest = TRUE))



#Plot Chnage in Arrival IDPs(FLow Arrival IDPs) in each round:
Total_ChangeIDPs_shp <- ggplot()+
  geom_sf(data=shp1)+
  geom_sf(aes(fill = Change_ArrivalIDPs_category, geometry = geometry), data= na.omit(merged_data_D_11)) +
  geom_sf(color = "#53534B", size=0.3) +
  scale_fill_viridis(discrete = TRUE, option = "magma",na.value= "lightgray", direction = -1) +
  labs(title = "Change in Number of Arrival IDPs by District",
       subtitle = "Map",
       caption = "Source: International Organization for Migration",
       fill = "Arrival IDPs") +
  theme_void()+
  print(Total_ChangeIDPs_shp)




#Total Change in arrival IDPs
ggplot(merged_data_D_11, aes(fill = Change_ArrivalIDPs_category, geometry = geometry)) +
  geom_sf(color = "#53534B", size = 0.03) +
  scale_fill_viridis(discrete = TRUE, option = "magma", na.value="lightgray", direction = -1) +
  labs(caption = 'Source: International Organization for Migration',
       fill = "Change In Arrival IDPs") +
  theme_void()






# Number of arrival IDPs by District
District_round_data2 <- aggregate(ArrivalIDPs ~ District + Round + HUMDelivered, data = mapdata_D_1, sum)
merged_data_D_2 <- merge(shp1, District_round_data2 [, c("District", "Round","ArrivalIDPs","HUMDelivered")], by = "District", all.x = TRUE)

# Create a new variable indicating the category
merged_data_D_2 <- merged_data_D_2 %>%
  mutate(ArrivalIDPs_category = cut(ArrivalIDPs,
                                    breaks = c(0, 5000, 10000, 20000, 30000, 40000, 50000, 60000, Inf),
                                    labels = c("0-5000", "5001-10000", "10001-20000", "20001-30000", "30001-40000", "40001-50000", "50001-60000", "60000+"),
                                    include.lowest = TRUE))




#Plot Total Arrival IDPs(Stock Arrival IDPs) in each round:
p_total_especies_shp <- ggplot()+
  geom_sf(data=shp1)+
  geom_sf(aes(fill = ArrivalIDPs_category), data= na.omit(merged_data_D_2)) +
  geom_sf(color = "#53534B", size=0.3) +
  scale_fill_viridis(discrete = TRUE, option = "magma",na.value= "gray", direction = -1) +
  labs(title = "Number of Arrival IDPs by Districts and Rounds",
       caption = "Data: International Organization for Migration",
       fill = "Arrival IDPs") +
  theme_void()+
  facet_wrap(~Round, ncol = 2, drop = TRUE)
print(p_total_especies_shp)




#Plot Total Arrival IDPs:
ggplot(merged_data_D_2, aes(fill = ArrivalIDPs_category, geometry = geometry)) +
  geom_sf(color = "#53534B", size = 0.03) +
  scale_fill_viridis(discrete = TRUE, option = "magma", na.value="lightgray", direction = -1) +
  labs(caption = 'Source: International Organization for Migration',
       fill = "Arrival IDPs") +
  theme_void()






#Another plot of Number of arrival IDPs per Districts
ggplot(merged_data_D_2, aes(fill = ArrivalIDPs_category, geometry = geometry)) +
  geom_sf(color = "gray", size = 0.3) +
  scale_fill_manual(
    values = c("0-5000" = "#ffffcc",    
               "5001-10000" = "#c7e9b4",  
               "10001-20000" = "#7fcdbb",
               "20001-30000" = "#41b6c4", 
               "30001-40000" = "#2c7fb8", 
               "40001-50000" = "#253494", 
               "50001-60000" = "#081d58", 
               "60000+" = "#000000"),    
    na.value = "lightgray"
  ) +
  labs(title = "Number of Arrival IDPs by District",
       caption = "Source: International Organization for Migration") +
  theme_bw() +
  theme(
    axis.text = element_blank(),
    axis.ticks = element_blank()
  )




#############################
## plot districts, Provinces and settlements
############################
settelements_sf <- st_as_sf(final_data, coords = c("Longitude", "Latitude"), crs = 4326)
st_crs(final_data)$epsg

final_data$Latitude <- as.numeric(final_data$Latitude)
final_data$Longitude <- as.numeric(final_data$Longitude)
final_data <- final_data[complete.cases(final_data$Longitude, final_data$Latitude), ]

library(sf)

plot(st_geometry(settelements_sf), axes = TRUE, pch = 20, col = "black", main = "Settlements", cex = 0.5)
pl3 <- ggplot() +
  geom_sf(data = shp1, aes(fill = District)) +
  geom_sf(data = settelements_sf, color = "black", shape = 20, size = 0.1) +
  ggthemes::theme_map() +
  theme(legend.position = "none") +
  labs(title = "Afghanistan District map with Settlements")

print(pl3)

#or

library(ggplot2)

# Assuming you have loaded the districts_sf and settelements_sf objects

pl3 <- ggplot() +
  geom_sf(data = shp1, fill = "white", color = "black") +
  geom_sf(data = settelements_sf, color = "blue", shape = 20, size = 0.1) +
  theme(legend.position = "none") +
  labs(title = "Afghanistan District map with Settlements")

print(pl3)


#################################################
#plot settlements received and Not received aids
##################################################


# Create a list of colors corresponding to HUMDelivered.
colors <- c("red", "green")

# Mapping numeric rounds to corresponding dates
settelements_sf$RoundDate <- factor(settelements_sf$Round, 
                                    levels = c(10, 11, 12, 13, 14, 16),
                                    labels = c("January 2020", "December 2020", "March 2021", 
                                               "June 2021", "December 2021", "December 2022"))

pl <- ggplot() +
  geom_sf(data = shp1, fill = "white", color = "black") +
  geom_sf(data = settelements_sf, aes(color = factor(HUMDelivered)), shape = 20, size = 0.5) +
  scale_color_manual(values = colors, labels = c("Not Received", "Received")) +
  theme(legend.position = "bottom") +
  labs(title = "Settlements Map") +
  facet_wrap(~RoundDate )+
  guides(color = guide_legend(override.aes = list(size = 4)))

print(pl)


# Create a list of colors corresponding to the existence of clinics
colors <- c("red", "green")

# Mapping numeric rounds to corresponding dates
settelements_sf$RoundDate <- factor(settelements_sf$Round, 
                                    levels = c(10, 11, 12, 13, 14, 16),
                                    labels = c("January 2020", "December 2020", "March 2021", 
                                               "June 2021", "December 2021", "December 2022"))

pl <- ggplot() +
  geom_sf(data = shp1, fill = "white", color = "black") +
  geom_sf(data = settelements_sf, aes(color = factor(HLTClinics)), shape = 20, size = 0.5) +
  scale_color_manual(values = colors, labels = c("Not Exist", "Exist")) +
  theme(legend.position = "bottom") +
  labs(title = "Settlements Map") +
  facet_wrap(~RoundDate )+
  guides(color = guide_legend(override.aes = list(size = 4)))

print(pl)


#####
#School with date included

# Create a list of colors corresponding to School Existence.
colors <- c("red", "green")

# Mapping numeric rounds to corresponding dates
settelements_sf$RoundDate <- factor(settelements_sf$Round, 
                                    levels = c(10, 11, 12, 13, 14, 16),
                                    labels = c("January 2020", "December 2020", "March 2021", 
                                               "June 2021", "December 2021", "December 2022"))

pl <- ggplot() +
  geom_sf(data = shp1, fill = "white", color = "black") +
  geom_sf(data = settelements_sf, aes(color = factor(EduSchoolExist)), shape = 20, size = 0.5) +
  scale_color_manual(values = colors, labels = c("School not existed", "School existed")) +
  theme(legend.position = "bottom") +
  labs(title = "Settlements Map by School Existence") +
  facet_wrap(~RoundDate)+
  guides(color = guide_legend(override.aes = list(size = 4)))


print(pl)


########################   Security-Situation  ##################################

# Create a list of colors corresponding to SecSituation values
sec_colors <- c("darkred", "red", "orange", "yellow", "lightgreen", "green")

# Mapping numeric rounds to corresponding dates
settelements_sf$RoundDate <- factor(settelements_sf$Round, 
                                    levels = c(10, 11, 12, 13, 14, 16),
                                    labels = c("January 2020", "December 2020", "March 2021", 
                                               "June 2021", "December 2021", "December 2022"))

# Define custom levels and labels for SecSituation
sec_levels <- c(0, 1, 2, 3, 4, 5)
sec_labels <- c("Not Applicable", "Very Insecure", "Insecure", "Neutral", "Secure", "Very Secure")

pl_sec <- ggplot() +
  geom_sf(data = shp1, fill = "white", color = "#53534B") +
  geom_sf(data = settelements_sf, aes(color = factor(SecSituation, levels = sec_levels)), shape = 20, size = 0.5) +
  scale_color_manual(values = sec_colors, 
                     labels = sec_labels) +
  theme_void()+
  theme(legend.position = "bottom") +
  facet_wrap(~RoundDate) +
  guides(color = guide_legend(override.aes = list(size = 4)))  # Adjust the 'size' parameter as needed

print(pl_sec)


# Convert merged_data1 to an sf object
merged_data_sf <- st_as_sf(merged_data1)

# Set CRS for merged_data_sf
st_crs(merged_data_sf) <- 4326  # Assuming the CRS is EPSG:4326


#######################################################################################################################
#point Plots
#######################################################################################################################



# Plotting for all 6 rounds with a smooth line and confidence interval(Stock Arrival IDPS)
ggplot(final_data, aes(y = HUMDelivered, x = ArrivalIDPs)) +
  geom_jitter(aes(color = factor(HUMDelivered)), alpha = 0.5) + 
  geom_smooth(method = "loess", se = TRUE, color = "red", size = 1.5) +  # Add a smooth line with confidence intervals
  scale_color_manual(values = c("lightpink", "lightgreen")) +  # Specify colors for the points
  xlim(0, 13000) +  # Set x-axis limit
  theme(legend.position = "bottom") +
  labs(color = "Humanitarian Aid") +  # Change legend title
  facet_wrap(~ Round)  # Separate plots for each Round


# Plotting for all 6 rounds with a smooth line and confidence interval(Flow Arrival IDPS)
ggplot(final_data, aes(y = HUMDelivered, x = Change_ArrivalIDPs)) +
  geom_jitter(aes(color = factor(HUMDelivered)), alpha = 0.5) + 
  geom_smooth(method = "loess", se = TRUE, color = "red", size = 1) +  # Add a smooth line with confidence intervals
  scale_color_manual(values = c("lightpink", "lightgreen")) +  # Specify colors for the points
  xlim(-500, 2000) +  # Set x-axis limit
  theme(legend.position = "bottom") +
  labs(color = "Humanitarian Aid") +  # Change legend title
  facet_wrap(~ Round)  # Separate plots for each Round

################################################################################################################
#World IDP's Map
#################################################################################################################


world <- ne_countries(scale = "medium", returnclass = "sf")
class(world)

world <- world[world$name != "Antarctica", ]

ggplot(data = world) +
  geom_sf()


Internal_Displacement <- read_excel("C:/Users/Downloads/Internal_Displacement.xlsx")


colnames(Internal_Displacement)[colnames(Internal_Displacement) == "ISO3"] <- "iso_a3"
world <- merge(world, Internal_Displacement[, c("iso_a3", "Displaced")], by = "iso_a3", all.x = TRUE)



# Find the range of total displacement values in the dataset
min_displacement <- min(Internal_Displacement$Total_Displacement, na.rm = TRUE)
max_displacement <- max(Internal_Displacement$Total_Displacement, na.rm = TRUE)


# Define breaks for the categories
breaks <- c(0, 100000, 500000, 1000000, 2000000, 3000000, 4000000, Inf)

ggplot(data = world) +
  geom_sf(aes(fill = Displaced)) +
  scale_fill_viridis_c(option = "plasma", trans = "sqrt", breaks = breaks, labels = scales::comma_format(scale = 1e-6)) +
  labs(fill = "Displaced (millions)") +  # Adjust legend label
  theme(legend.position = "bottom") +  # Move legend to the bottom
  theme(legend.key.width = unit(1, "cm"))  # Adjust legend width


# Plotting the density of points together with the shapefile
combined_plot <- ggplot() +
  geom_sf(data = shp1, fill = "white", color = "black") +
  stat_density_2d(data = final_data, aes(x = Longitude, y = Latitude, fill = ..level..), 
                  geom = "polygon", contour = TRUE, alpha = 0.5) +
  scale_fill_viridis_c() +
  theme_minimal() +
  labs(title = "ArrivalIDPs Density Map", x = "Longitude", y = "Latitude", fill = "Density")

# Print the combined plot to verify it
print(combined_plot)
