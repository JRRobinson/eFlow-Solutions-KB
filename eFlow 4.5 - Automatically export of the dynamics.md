## eFlow 4.5 - Automatically export of the dynamics ##

### Question / Description: ###
A customer is asking if eFlow 4.5 can automatically export the dynamic additionally to the xml in the ERPExport?
Has one of you implemented this in the past?

### Answer / Solution: ###
For all who are interested in Maciej send me a short customization for example in OnPostGetCollections or OnPrePutCollections:

    string sDynamicFile=Path.Combine(oCSM.PathLocator.get_Path("Workdir")),
    string Format("{0}_{1}.Dynamic",oCollData.Name, DateTime.Now.ToString("yyyyMMddHHmmss")));
    oCSM.DynamicImportExport.ExportCollectionsByName(sDynamicFile, new string[] {oCollData.Name});