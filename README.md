RobustHaven.IntegrationTests
============================

RobustHaven.IntegrationTests is used for integration tests with WatiN or Selenium


<code>

        [TestMethod]
        [DeploymentItem(".IntegrationTests\\TestData\\CheckItemsMultiCompany.csv")]
        [DataSource("Microsoft.VisualStudio.TestTools.DataSource.CSV", "|DataDirectory|\\CheckItemsMultiCompany.csv", "CheckItemsMultiCompany#csv", DataAccessMethod.Sequential)]
        public void Check_View_Items_MultiCompany()
        {
            var workflow = new Sequence(
                new Login() 
                { 
                    UserName = testContext.DataRow["UserName"].ToString(), Password = password 
                },
                new Sequence(
                    new AddItem()
                    {
                        CompanyName = testContext.DataRow["CompanyName"].ToString(),
                        FirstName = DateTime.Now.DayOfWeek.ToString(),
                        LastName = String.Format("{0}Quote{1}", testContext.DataRow["ItemName"], DateTime.Now.DayOfYear),
                        TaxId = taxId.Next(100000000, 999999999).ToString()
                    },
                    new Sequence (
                        new AddDetailItem() 
                        { 
                            Item = String.Format("{0}Quote{1}", testContext.DataRow["ItemName"], DateTime.Now.DayOfYear),
                            ItemFullName = String.Format("{0} {1}Quote{2}", DateTime.Now.DayOfWeek, testContext.DataRow["ItemName"], DateTime.Now.DayOfYear),
                            State = "Arkansas",
                            IsPolicy = false
                        },
                        new CheckViewItemsMultiCompany()
                        {
                            CompanyName = testContext.DataRow["CompanyName"].ToString(),
                            HasAccessOne = Convert.ToBoolean(testContext.DataRow["HasAccessOne"].ToString()),
                            HasAccessTwo = Convert.ToBoolean(testContext.DataRow["HasAccessTwo"].ToString())
                        })));

            using (var context = workflow.Setup(config))
            {
                context.Execute();
            }
        }

</code>