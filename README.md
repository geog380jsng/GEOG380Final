# GEOG380Final
#Project Code

import arcpy
from arcpy import env
env.workspace = "C:\GEOG_380\FinalProject"
env.workspace = "C:\Users\yours\Desktop"
 
#Use AddField_management tool to create a new field called “Rating”
#Use Cursor to access "ANN_GHI_RN"(annual solar generation average) row
#User can change criteria of Ratings to suit their own preferences of energy generation kWh/㎡
 
arcpy.AddField_management("USSRP","Rating","TEXT")
cursor = arcpy.da.UpdateCursor("USSRP", ["ANN_GHI_RN","Rating"])
for row in cursor:
     if row[0] >= 5.1:
         row[1] = "Extremely Suitable"
         cursor.updateRow(row)
     elif row[0] > 4.0 and row[0] < 5.1:
         row[1] = "Suitable"
         cursor.updateRow(row)
     else:
         row[1] = "Not Suitable"
         cursor.updateRow(row)
 del cursor
 
#Model will only use Extremely Suitable and Suitable rankings 
#User can define which Energy Rankings they wish to use
 
ExtSuit = " \"Rating\" = 'Extremely Suitable'"
Suit = " \"Rating\" = 'Suitable'"
 
#Select_analysis will select Extremely Suitable locations for Solar energy potential
#SelectLayerByLocation_management tool will select Electric Substations within Extremely Suitable Rankings
#CopyFeatures_management will create a new layer of Electric Substations in Extremely Suitable Locations for Solar Energy Potential
#User can modify the desired substations in specified Rankings
 
arcpy.Select_analysis("USSRP","/ExtSuitSelect.shp", ExtSuit)
arcpy.SelectLayerByLocation_management("Electric_Subs", "INTERSECT", "C:\Users\yours\Desktop\ExtSuitSelect.shp", "", "NEW_SELECTION")
arcpy.CopyFeatures_management(“Electric_Subs”, “Electric_Subs_ExtSuit”)
 
 
 
#Select_analysis will select Suitable locations for Solar energy potential
#SelectLayerByLocation_management tool will select Electric Substations within Suitable Rankings
#CopyFeatures_management will create a new layer of Electric Substations in Suitable Locations for Solar Energy Potential
#User can modify the desired substations in specified Rankings
 
arcpy.Select_analysis("USSRP","/SuitSelect.shp", Suit)
arcpy.SelectLayerByLocation_management("Electric_Subs", "INTERSECT", "C:\Users\yours\Desktop\GEOG380\GEOGFinalProject\USSRP\SuitSelect", "", "NEW_SELECTION")
arcpy.CopyFeatures_management(“Electric_Subs”, “Electric_Subs_Suit”)
 
