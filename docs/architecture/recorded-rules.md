# Recorded Legacy Rules

## Purpose and decision boundary

This register translates every bullet in
`Source/ChuongTrinh/VersionConfig.xml` into traceable modernization evidence.
It records what a release note implies; it does not decide that the implied
behavior belongs in the new product.

BUG-001 classifies the source evidence. BUG-003 requires a domain expert to
decide whether each rule is still required, changed, or obsolete. No row marked
`Rule — pending BUG-003` may enter implementation as an approved requirement.

## Classification

| Status | Meaning |
|---|---|
| `Rule — pending BUG-003` | The entry states or implies observable behavior, validation, data handling, or output. It remains a hypothesis until domain review. |
| `Cosmetic/unspecified — closed` | The entry is a heading, vague “other fixes” note, or lacks enough behavior to create a rule. Later source/history evidence may add a separate, specific row. |
| `Obsolete implementation — closed` | The entry is tied to the retired Office/Access/desktop implementation rather than a user outcome. Any underlying outcome is captured by another rule row. |

“English / implied rule” is a faithful explanation, not a word-for-word legal
translation. Ellipses and apparent spelling mistakes in the Vietnamese column
are preserved from the source.

## Changelog register

| ID | Version | Source line | Original Vietnamese | English / implied rule | Module | Status |
|---|---:|---:|---|---|---|---|
| CHG-001 | 3.3.7 | 6 | Hiển thị thêm thông tin qua đời trong màn hình danh sách giáo dân. | Show death information in the parishioner list. | People / list | Rule — pending BUG-003 |
| CHG-002 | 3.3.7 | 7 | Hiển thị Tên Cha, Tên Mẹ, Ngày XTRL vào danh sách học sinh giáo lý. | Show father, mother, and First Communion date in the catechism-student list. | Catechism / list | Rule — pending BUG-003 |
| CHG-003 | 3.3.7 | 8 | Sửa một số lỗi khi import dữ liệu. | Unspecified import fixes; no reproducible case is recorded. | Import | Cosmetic/unspecified — closed |
| CHG-004 | 3.3.7 | 9 | Cho phép chọn giáo xứ khi import từ phần mềm MGC nếu trong DataGx.accdb có nhiều hơn một giáo xứ. | When an MGC source contains multiple parishes, require the operator to select the parish to import. | Import / MGC | Rule — pending BUG-003 |
| CHG-005 | 3.3.7 | 10 | Thêm thông tin tên người vợ/chồng vào mẫu in Lý lịch cá nhân. | Include the spouse's name in the personal-profile printout. | Reports / personal profile | Rule — pending BUG-003 |
| CHG-006 | 3.3.7 | 11 | Cho phép nhập ngày bí tích sau ngày hiện tại trong màn hình nhập giáo dân. Chỉ hiện cảnh báo khi lưu dữ liệu giáo dân. | Permit a future sacrament date, but warn when the parishioner record is saved rather than blocking entry. | People / sacraments | Rule — pending BUG-003 |
| CHG-007 | 3.3.7 | 12 | Một số sửa lỗi và cập nhật khác. | Other fixes and updates are not specified. | Cross-cutting | Cosmetic/unspecified — closed |
| CHG-008 | 3.3.6 | 15 | Thêm thông tin bao đồng, GL Hôn nhân vào trong chi tiết giáo dân. | Show Solemn Communion and marriage-catechism information in parishioner details. | People / catechism | Rule — pending BUG-003 |
| CHG-009 | 3.3.6 | 16 | Sửa một số lỗi khi import dữ liệu từ Excel. | Unspecified Excel-import fixes; no reproducible case is recorded. | Import / Excel | Cosmetic/unspecified — closed |
| CHG-010 | 3.3.6 | 17 | Và một số sửa lỗi cùng cải tiến khác. | Other fixes and improvements are not specified. | Cross-cutting | Cosmetic/unspecified — closed |
| CHG-011 | 3.3.5 | 20 | Sửa lỗi không hiển thị được tên vợ chồng trong màn hình thống kê hôn phối. | Marriage statistics must display both spouses' names. | Statistics / marriage | Rule — pending BUG-003 |
| CHG-012 | 3.3.4 | 23 | Các cải tiến và sửa lỗi: | Heading for improvements and fixes; no independent behavior. | Cross-cutting | Cosmetic/unspecified — closed |
| CHG-013 | 3.3.4 | 24 | Màn hình chọn giáo dân (vào gia đình, sổ bí tích hoặc lớp giáo lý...) mặc định sẽ không hiển thị hết tất cả các giáo dân trong giáo họ được chọn mà cho phép người dùng nhập tên giáo dân cần tìm kiếm, chương trình sẽ tìm kiếm trong cả hồ sơ lưu trữ. | Person pickers should start without loading an entire sub-parish; name search spans active and archived people. | People / search | Rule — pending BUG-003 |
| CHG-014 | 3.3.4 | 25 | Màn hình bí tích chi tiết mặc định được sắp xếp theo số bí tích | Sort sacrament-detail records by sacrament number by default. | Sacraments / list | Rule — pending BUG-003 |
| CHG-015 | 3.3.4 | 26 | Cải tiến chức năng nhập dữ liệu từ chương trình MGC để nhập được dữ liệu cho nhiều trường hợp hơn. | MGC import was broadened, but the supported cases are not identified. | Import / MGC | Cosmetic/unspecified — closed |
| CHG-016 | 3.3.4 | 27 | Màn hình tìm kiếm giáo dân được thêm mục "Ngày sinh" và "Địa chỉ". | Parishioner search must support date of birth and address criteria. | People / search | Rule — pending BUG-003 |
| CHG-017 | 3.3.4 | 28 | Thêm địa chỉ và số điện thoại vào một số mẫu báo cáo. | Reports that identify people or households may require address and phone fields; exact templates need report inventory. | Reports | Rule — pending BUG-003 |
| CHG-018 | 3.3.4 | 29 | Thống kê hôn phối bị báo lỗi trong một số trường hợp. | Marriage statistics must complete for the previously failing cases, but no case is recorded. | Statistics / marriage | Rule — pending BUG-003 |
| CHG-019 | 3.3.4 | 30 | Một số trường hợp thống kê không in được danh sách kết quả sau khi thống kê. | Every successful statistics result must remain printable. | Statistics / export | Rule — pending BUG-003 |
| CHG-020 | 3.3.4 | 31 | Và một số sửa lỗi cùng cải tiến khác | Other fixes and improvements are not specified. | Cross-cutting | Cosmetic/unspecified — closed |
| CHG-021 | 3.3.3 | 34 | Các cải tiến: | Heading for improvements; no independent behavior. | Cross-cutting | Cosmetic/unspecified — closed |
| CHG-022 | 3.3.3 | 35 | Cho phép xem gia đình từ màn hình cá nhân giáo dân. | A parishioner record should navigate to its household record. | People / households | Rule — pending BUG-003 |
| CHG-023 | 3.3.3 | 36 | Đánh dấu những thành viên trong gia đình ở ngoài xứ và không tính trong số lượng thành viên gia đình. | Visually identify household members outside the parish and exclude them from the displayed household count. | Households | Rule — pending BUG-003 |
| CHG-024 | 3.3.3 | 37 | Cho phép xóa hình ảnh gia đình, cá nhân. | Authorized users may remove household and person photos. | People / households / photos | Rule — pending BUG-003 |
| CHG-025 | 3.3.3 | 38 | Khi thêm một hôn phối, nếu cặp hôn phối đó chưa có gia đình, chương trình sẽ hỏi có muốn thêm gia đình mới cho cặp hôn phôi mới hay không. | After recording a marriage for a couple without a household, offer to create their household. | Marriage / households | Rule — pending BUG-003 |
| CHG-026 | 3.3.3 | 39 | Sửa mẫu in Phiếu Gia Đình bằng EXCEL và WORD. | The household-sheet templates were changed, but the intended output difference is not recorded. | Reports / household sheet | Cosmetic/unspecified — closed |
| CHG-027 | 3.3.3 | 40 | Khi đánh dấu một người qua đời, chương trình sẽ tìm người vợ/chồng của người đó để đánh dấu người vợ/chồng đó trở về tình trạng độc thân. | When a married person is marked deceased, locate the spouse and return the spouse to a non-married/single status. | People / marriage | Rule — pending BUG-003 |
| CHG-028 | 3.3.3 | 42 | Sửa các lỗi: | Heading for bug fixes; no independent behavior. | Cross-cutting | Cosmetic/unspecified — closed |
| CHG-029 | 3.3.3 | 43 | Khi in phiếu gia đình, nếu chọn mục in người chứng hôn phối sẽ xuất hiện báo lỗi. | A household sheet with the marriage-witness option must generate without error. | Reports / household sheet | Rule — pending BUG-003 |
| CHG-030 | 3.3.3 | 44 | Sửa in phiếu lí lịch cá nhân in nhầm dữ liệu số rửa tội vào ô số rước lễ. | The personal profile's First Communion number must not be populated with the baptism number. | Reports / personal profile | Rule — pending BUG-003 |
| CHG-031 | 3.3.3 | 45 | Thống kê hôn phối không hiện được thông tin giáo họ, tên vợ chồng bị thay thế bằng mã giáo dân. | Marriage statistics must show the sub-parish and spouse names, never substitute person IDs for names. | Statistics / marriage | Rule — pending BUG-003 |
| CHG-032 | 3.3.3 | 46 | Thêm giáo dân bị lỗi khi đang lọc trên lưới giáo dân trong màn hình danh sách giáo dân. | Adding a parishioner while the list is filtered must succeed and preserve consistent list state. | People / list | Rule — pending BUG-003 |
| CHG-033 | 3.3.3 | 47 | Với gia đình có nhiều hơn 8 thành viên, in phiếu gia đình sẽ không in ra đủ 8 thành viên. | A household with more than eight members must print the intended first/full set rather than fewer than eight due to the prior bug; pagination policy needs review. | Reports / household sheet | Rule — pending BUG-003 |
| CHG-034 | 3.3.3 | 48 | Thống kê người đã lập gia đình không chính xác. Có trường hợp cá nhân đã có hôn phối nhưng chương trình vẫn không chọn mục "Có gia đình" | A person with a qualifying marriage must be classified consistently as having a family in statistics. | Statistics / marriage | Rule — pending BUG-003 |
| CHG-035 | 3.3.3 | 49 | Cùng một số sửa lỗi và cải tiến khác. | Other fixes and improvements are not specified. | Cross-cutting | Cosmetic/unspecified — closed |
| CHG-036 | 3.3.2 (2020-03-30) | 52 | Sửa lỗi khi in phiếu gia đình, nếu chọn mục in người chứng hôn phối sẽ xuất hiện báo lỗi. | Duplicate of CHG-029: household-sheet generation with marriage witnesses must not fail. | Reports / household sheet | Rule — pending BUG-003 |
| CHG-037 | 3.3.2 (2020-03-30) | 53 | Sửa lỗi thống kê hôn phối không hiện được thông tin giáo họ, tên vợ chồng bị thay thế bằng mã giáo dân. | Duplicate of CHG-031: show sub-parish and spouse names in marriage statistics. | Statistics / marriage | Rule — pending BUG-003 |
| CHG-038 | 3.3.2 (2020-03-30) | 54 | Và một số lỗi khác. | Other fixes are not specified. | Cross-cutting | Cosmetic/unspecified — closed |
| CHG-039 | 3.3.2 (2020-03-15) | 57 | Trong gia đình hoặc hôn phối, khi 1 người mất đi, người còn lại sẽ được tự bỏ chọn mục đã lập gia đình (trở thành độc thân). | Duplicate/expansion of CHG-027: a surviving spouse should no longer be flagged as married after the partner dies. | People / marriage | Rule — pending BUG-003 |
| CHG-040 | 3.3.2 (2020-03-15) | 58 | Thêm tùy chọn vào mục "Tùy chọn" cho việc in phiếu gia đình, cho chọn in cả những người đã đưa vào hồ sơ lưu trữ hay không. | Household-sheet generation must provide an explicit option to include or exclude archived people. | Reports / household sheet | Rule — pending BUG-003 |
| CHG-041 | 3.3.2 (2020-03-15) | 59 | Thêm địa chỉ, số điện thoại vào mẫu in phiếu gia đình và các giáy giới thiệu. | Include address and phone in the household sheet and introduction documents. | Reports | Rule — pending BUG-003 |
| CHG-042 | 3.3.2 (2020-03-15) | 60 | Sửa mẫu in phiếu gia đình để cho phép in nơi nhận bí tích bên dưới ngày nhận bí tích. | Household sheets may print the sacrament place beneath each sacrament date. | Reports / household sheet | Rule — pending BUG-003 |
| CHG-043 | 3.3.2 (2020-03-15) | 61 | Cho phép import dữ liệu MGC phiên bản Access 2007 trở lên. Cần cài thêm "Microsoft Access Database Engine 2010 Redistributable" | The desktop importer used the Access Database Engine to read Access 2007+ MGC files. | Import / MGC | Obsolete implementation — closed |
| CHG-044 | 3.3.2 (2020-03-15) | 62 | Sửa một số lỗi khác. | Other fixes are not specified. | Cross-cutting | Cosmetic/unspecified — closed |
| CHG-045 | 3.3.0 | 65 | Cập nhật chức năng: | Heading for feature updates; no independent behavior. | Cross-cutting | Cosmetic/unspecified — closed |
| CHG-046 | 3.3.0 | 66 | Màn hình danh sách gia đình: | Heading for household-list changes; no independent behavior. | Households / list | Cosmetic/unspecified — closed |
| CHG-047 | 3.3.0 | 67 | Khi chọn nhiều gia đình trên lưới, bấm phím "In phiếu gia đình", chương trình sẽ hỏi in chung tất cả các gia đình được chọn vào 1 file hoặc in thành nhiều file riêng. | For multiple selected households, ask whether to produce one combined file or separate files. | Reports / household sheet | Rule — pending BUG-003 |
| CHG-048 | 3.3.0 | 68 | Nếu người nam hoặc người nữ đã qua đời, chương trình sẽ gạch ngang tên trong danh sách gia đình. | Strike through a deceased husband or wife in the household list. | Households / list | Rule — pending BUG-003 |
| CHG-049 | 3.3.0 | 69 | Màn hình nhập gia đình: | Heading for household-entry changes; no independent behavior. | Households / edit | Cosmetic/unspecified — closed |
| CHG-050 | 3.3.0 | 70 | Khi thêm 1 giáo dân vào làm 1 thành viên của 1 gia đình (không phải vai trò là vợ chồng) nhưng giáo dân đó đã thuộc 1 thành viên của 1 gia đình khác, chương trình sẽ đưa ra thông báo "giáo dân … đã thuộc gia đình… vui lòng xóa giáo dân … ra khỏi gia đình … trước khi thêm". | Reject adding a non-spouse household member who already belongs to another household, and identify the conflicting membership. | Households / validation | Rule — pending BUG-003 |
| CHG-051 | 3.3.0 | 71 | Mỗi giáo dân chỉ có thể là người nam (chồng) hoặc người nữ (vợ) của một gia đình mà thôi. Nếu thêm trùng người nam hoặc người nữ vào nhiều gia đình, chương trình sẽ báo lỗi. Trừ khi gia đình cũ đã được đưa vào hồ sơ lưu trữ. | A person may be husband or wife in only one active household; an archived former household is exempt. | Households / validation | Rule — pending BUG-003 |
| CHG-052 | 3.3.0 | 72 | Khi chọn người nam/ người nữ mà đã có hôn phối, chương trình tự chọn người còn lại vào màn hình nhập gia đình kèm theo thông tin hôn phối. | Selecting a married husband/wife for a household should select the spouse and carry the marriage details. | Households / marriage | Rule — pending BUG-003 |
| CHG-053 | 3.3.0 | 73 | Màn hình hội đoàn: ngày bổn mạng chỉ cần nhập ngày tháng, không nhập năm | An association patronal feast date stores day and month without requiring a year. | Associations / partial dates | Rule — pending BUG-003 |
| CHG-054 | 3.3.0 | 74 | Chứng nhận hôn phối thêm tùy chọn in ngày rao hôn phối, thêm thông tin bí tích thêm sức, tên giáo họ và địa chỉ của từng người trong đôi hôn phối. | Marriage certificates may include bann dates and must support confirmation, sub-parish, and address details for each spouse. | Reports / marriage certificate | Rule — pending BUG-003 |
| CHG-055 | 3.3.0 | 75 | Màn hình lớp giáo lý: cho sửa số thứ tự học viên trong lớp. | Allow editing a student's ordering number within a catechism class. | Catechism / class | Rule — pending BUG-003 |
| CHG-056 | 3.3.0 | 76 | Các mẫu in bằng file WORD, bỏ các dấu ...... (trong tương lai, sẽ cố gắng để dấu ...... trong trường hợp không có dữ liệu tại mục cần in). | The Word templates removed dotted placeholders; this formatting technique is not a modern requirement. | Reports / Word templates | Obsolete implementation — closed |
| CHG-057 | 3.3.0 | 77 | Thêm địa chỉ vào màn hình Tìm giáo dân | Show address in parishioner search. | People / search | Rule — pending BUG-003 |
| CHG-058 | 3.3.0 | 79 | Sửa lỗi: | Heading for bug fixes; no independent behavior. | Cross-cutting | Cosmetic/unspecified — closed |
| CHG-059 | 3.3.0 | 80 | Màn hình thống kê: Khi có kết quả thống kê, nút "In danh sách" bị vô hiệu hóa, không bấm vào được. | Enable printing whenever statistics returned a printable result. | Statistics / export | Rule — pending BUG-003 |
| CHG-060 | 3.3.0 | 81 | Màn hình nhập giáo dân: Không lưu được số CMND | Persist the person's identity-card number when the record is saved. | People / identity | Rule — pending BUG-003 |
| CHG-061 | 3.3.0 | 82 | Màn hình lớp giáo lý: Không xóa được học viên ra khỏi lớp giáo lý. | Authorized users must be able to remove a student from a catechism class. | Catechism / class | Rule — pending BUG-003 |
| CHG-062 | 3.3.0 | 83 | Sửa một số lỗi gây ra việc nhập dữ liệu từ Excel không thành công. | A valid Excel import should complete, but the previously failing cases are not identified. | Import / Excel | Rule — pending BUG-003 |
| CHG-063 | 3.3.0 | 85 | Cùng một số chỉnh sửa và cải tiến khác. | Other adjustments and improvements are not specified. | Cross-cutting | Cosmetic/unspecified — closed |
| CHG-064 | 3.2.2 | 89 | Thêm chức năng quản lý Hội đoàn. | Provide association management. Detailed behavior must come from source/workflow inventory. | Associations | Rule — pending BUG-003 |
| CHG-065 | 3.2.2 | 91 | Cập nhật chức năng quản lý thông tin Giáo Dân. | Heading for parishioner-management changes; no independent behavior. | People | Cosmetic/unspecified — closed |
| CHG-066 | 3.2.2 | 92 | Thêm ràng buộc nếu đã có nhập ngày rửa tội thì bắt buộc phải có tên thánh. | If a baptism date is entered, baptismal/saint name is required. | People / sacraments / validation | Rule — pending BUG-003 |
| CHG-067 | 3.2.2 | 93 | Thêm 1 tab Hội đoàn có chức năng thêm giáo dân vào hội đoàn hoặc xem lại lịch sử tham gia hội đoàn của giáo dân. | From a parishioner record, add association membership and view membership history. | People / associations | Rule — pending BUG-003 |
| CHG-068 | 3.2.2 | 94 | Sửa lỗi bấm phím tab ở các ô nhập ngày tháng, con trỏ không nhảy đến ô nhập tiếp theo. | Date inputs must participate correctly in keyboard tab order. | UI / date input | Rule — pending BUG-003 |
| CHG-069 | 3.2.2 | 95 | Sửa lỗi một số trường hợp một giáo dân đã được chọn lập gia đình nhưng mục "Đã có gia đình" không được chọn trong màn hình thông tin giáo dân. | A qualifying household/marriage selection must keep the person's “has family” status consistent. | People / households | Rule — pending BUG-003 |
| CHG-070 | 3.2.2 | 97 | Cập nhật chức năng quản lý thông tin Gia Đình. | Heading for household-management changes; no independent behavior. | Households | Cosmetic/unspecified — closed |
| CHG-071 | 3.2.2 | 98 | Sửa lỗi khi người nam trong gia đình đã mất, chọn người nữ làm chủ hộ, chương trình luôn thông báo chọn chủ hộ khi cập nhật gia đình. | A surviving wife selected as head must satisfy household-head validation. | Households / validation | Rule — pending BUG-003 |
| CHG-072 | 3.2.2 | 99 | Sửa lỗi khi chọn một thành viên trong gia đình lên làm chủ hộ, chương trình hiển thị lỗi khi lưu. | Promoting an eligible household member to head must save successfully. | Households / validation | Rule — pending BUG-003 |
| CHG-073 | 3.2.2 | 100 | Ràng buộc số tuổi phải lớn hơn 14 tuổi mới cho phép hôn phối hoặc thêm vào làm người nam, người nữ trong gia đình. Nếu người nam hoặc người nữ dưới 18 tuổi sẽ hiển thị cảnh báo. | Block marriage/husband/wife assignment at age 14 or younger; warn when either spouse is under 18. Exact age calculation and policy require review. | Marriage / households / validation | Rule — pending BUG-003 |
| CHG-074 | 3.2.2 | 101 | Thêm một số ràng buộc khác về việc một thành viên thuộc nhiều gia đình. | Other multi-household membership constraints are not specified. | Households / validation | Cosmetic/unspecified — closed |
| CHG-075 | 3.2.2 | 102 | Chỉ gạch bỏ những thành viên trong gia đình nếu thành viên đó đã được lập một gia đình mới với vai trò là người nam hoặc người nữ trong màn hình nhập gia đình. | Strike through a household member only when the person is husband or wife in a newly created household. | Households / list | Rule — pending BUG-003 |
| CHG-076 | 3.2.2 | 103 | Khi thêm người nam hoặc người nữ vào màn hình nhập gia đình, nếu không có thông tin hôn phối thì không tự động cập nhật trạng thái đã lập gia đình cho người nam hoặc người nữ. | Assigning someone as husband/wife without marriage information must not automatically mark that person as married. | Households / marriage | Rule — pending BUG-003 |
| CHG-077 | 3.2.2 | 104 | Cập nhật lại chức năng chuyển xứ cho gia đình: Nếu thành viên gia đình đã có gia đình riêng hoặc đã vào danh sách lưu trữ thì sẽ không chuyển xứ cùng gia đình. | During household transfer, do not transfer members who have their own household or are archived. | Transfers / households | Rule — pending BUG-003 |
| CHG-078 | 3.2.2 | 105 | Cập nhật lại cách tính số lượng thành viên trong gia đình ở màn hình danh sách gia đình. | Heading for the household-count rules in CHG-079 and CHG-080. | Households / count | Cosmetic/unspecified — closed |
| CHG-079 | 3.2.2 | 106 | Đếm tất cả các thành viên với mọi vai trò. Trước đây chương trình chỉ tính các thành viên có vài trò là con. | Count eligible household members in every role, not only children. | Households / count | Rule — pending BUG-003 |
| CHG-080 | 3.2.2 | 107 | Không đếm các thành viên đã chuyển xứ, qua đời, có gia đình và ở trong gia đình khác (bị gạch đỏ trong danh sách thành viên gia đình). | Exclude transferred, deceased, or separately housed/married members from the household count. | Households / count | Rule — pending BUG-003 |
| CHG-081 | 3.2.2 | 109 | Quản lý giáo lý: | Heading for catechism changes; no independent behavior. | Catechism | Cosmetic/unspecified — closed |
| CHG-082 | 3.2.2 | 110 | Cho phép cập nhật thông tin giáo dân cho danh sách các học viên trong một lớp giáo lý. | Allow updating parishioner information for students listed in a catechism class. | Catechism / people | Rule — pending BUG-003 |
| CHG-083 | 3.2.2 | 112 | Màn hình thống kê: | Heading for statistics changes; no independent behavior. | Statistics | Cosmetic/unspecified — closed |
| CHG-084 | 3.2.2 | 113 | Sửa lỗi sau khi thống kê xong thì không xuất được danh sách kết qua ra Excel. | A completed statistics result must be exportable. The modern format is decided separately. | Statistics / export | Rule — pending BUG-003 |
| CHG-085 | 3.2.1 | 116 | Sửa lỗi khi cập nhật gia đình/giáo dân thì bị mất hình ảnh đã lưu trước đó. | Updating a person or household must preserve its existing photo unless removal/replacement is explicit. | People / households / photos | Rule — pending BUG-003 |
| CHG-086 | 3.2.1 | 117 | Chỉnh sửa mẫu in "Phiếu gia đình" trên MS Word cho giống với các mẫu trên MS Excel. | Household-sheet outputs should have equivalent required content/layout across supported output paths. | Reports / household sheet | Rule — pending BUG-003 |
| CHG-087 | 3.2.1 | 118 | Trước đây, các tùy chọn cho phiếu gia đình chỉ có tác dụng với mẫu trên MS Excel. | Household-sheet options should affect every supported output path consistently. | Reports / household sheet | Rule — pending BUG-003 |
| CHG-088 | 3.2.1 | 119 | Sửa lỗi mẫu in "Phiếu gia đình" trên MS Word không in ra được Mã giáo dân khi chọn tùy chọn "Thay số thứ tự bằng mã giáo dân". | When configured to replace sequence number with person ID, the household sheet must print the person ID. | Reports / household sheet | Rule — pending BUG-003 |
| CHG-089 | 3.2.1 | 120 | Thêm các tùy chọn cho "Phiếu gia đình" như: | Heading for household-sheet options in CHG-090 through CHG-093. | Reports / household sheet | Cosmetic/unspecified — closed |
| CHG-090 | 3.2.1 | 121 | In số bí tích bên dưới ngày nhận bí tích. | Household sheets may print each sacrament number beneath its date. | Reports / household sheet | Rule — pending BUG-003 |
| CHG-091 | 3.2.1 | 122 | In người đã đầu bên dưới ngày nhận bí tích. | Household sheets may print the sponsor beneath the sacrament date. | Reports / household sheet | Rule — pending BUG-003 |
| CHG-092 | 3.2.1 | 123 | In người chứng hôn phối bên dưới ngày hôn phối. | Household sheets may print marriage witnesses beneath the marriage date. | Reports / household sheet | Rule — pending BUG-003 |
| CHG-093 | 3.2.1 | 124 | In tên cha mẹ (chỉ có với mẫu MS Excel). | Household sheets may print parents' names; the legacy limitation to Excel is not preserved. | Reports / household sheet | Rule — pending BUG-003 |
| CHG-094 | 3.2.1 | 125 | Phiếu gia đình luôn in ra các dòng trống cho đủ 8 dòng nếu số lượng thành viên trong gia đình không đủ 8 người. | A household sheet pads the member area to eight rows when fewer than eight members are printed. | Reports / household sheet | Rule — pending BUG-003 |
| CHG-095 | 3.2.1 | 126 | Hiện tại mẫu in trên MS Word chưa được hoàn hảo như mẫu in trên Excel, tốc độ xuất phiếu gia đình trên MS Word cũng chậm hơn, khuyến khích sử dụng mẫu Excel cho phiếu gia đình. | Word was slower and less complete than Excel, so Excel was recommended; this implementation comparison is retired. | Reports / Office implementation | Obsolete implementation — closed |
| CHG-096 | 3.2 | 129 | Sửa lỗi in Phiếu gia đình in cả những người đã đưa vào hồ sơ lưu trữ hoặc đã lập gia đình. | By default, exclude archived members and members who formed another family from the household sheet. | Reports / household sheet | Rule — pending BUG-003 |
| CHG-097 | 3.2 | 130 | Sửa mẫu in Phiếu gia đình trên MS Word. | The Word template was changed, but the intended output difference is not recorded. | Reports / Word templates | Cosmetic/unspecified — closed |
| CHG-098 | 3.2 | 131 | Cho phép chọn mẫu in Phiếu gia đình bằng MS Word hoặc MS Excel. | Choosing between Word and Excel templates is a retired desktop implementation choice. | Reports / Office implementation | Obsolete implementation — closed |
| CHG-099 | 3.1 | 134 | Cho phép quản trị người dùng với nhiều vai trò khác nhau. | Support user administration with multiple roles. Role definitions and permissions require separate review. | Accounts / authorization | Rule — pending BUG-003 |
| CHG-100 | 3.1 | 135 | Cập nhật tính năng hiển thị ảnh cho cá nhân và gia đình. | Display photos for people and households. | People / households / photos | Rule — pending BUG-003 |
| CHG-101 | 3.1 | 136 | Sửa lỗi đếm số thành viên trong gia đình (loại bỏ những thành viên đã có gia đình hoặc đã mất) | Exclude members who formed another family or died from the household count. | Households / count | Rule — pending BUG-003 |
| CHG-102 | 3.1 | 137 | Cập nhật thêm một số loại thống kê : | Heading for statistics types in CHG-103 through CHG-109. | Statistics | Cosmetic/unspecified — closed |
| CHG-103 | 3.1 | 138 | Danh sách chủ hộ | Provide a list/statistic of household heads. | Statistics / households | Rule — pending BUG-003 |
| CHG-104 | 3.1 | 139 | Danh sách gia trưởng | Provide the legacy “gia trưởng” demographic list; exact definition requires domain review. | Statistics / demographics | Rule — pending BUG-003 |
| CHG-105 | 3.1 | 140 | Danh sách hiền mẫu | Provide the legacy “hiền mẫu” demographic list; exact definition requires domain review. | Statistics / demographics | Rule — pending BUG-003 |
| CHG-106 | 3.1 | 141 | Danh sách cao niên | Provide the senior/elderly people list; age threshold and date precision require review. | Statistics / demographics | Rule — pending BUG-003 |
| CHG-107 | 3.1 | 142 | Danh sách giới trẻ | Provide the youth list; age threshold and date precision require review. | Statistics / demographics | Rule — pending BUG-003 |
| CHG-108 | 3.1 | 143 | Danh sách thiếu nhi | Provide the children list; age threshold and date precision require review. | Statistics / demographics | Rule — pending BUG-003 |
| CHG-109 | 3.1 | 144 | Danh sách gia đình theo từng tình trạng hôn phối | Provide household lists grouped/filtered by marriage status. | Statistics / households | Rule — pending BUG-003 |
| CHG-110 | 3.1 | 145 | Cập nhật tính năng : quản lý tài khoản người dùng, bảo mật | Provide account administration and security, but the entry does not define controls or permissions. | Accounts / security | Rule — pending BUG-003 |
| CHG-111 | 3.1 | 146 | Bổ sung cho chức năng "Giới thiệu hôn phối": khi làm giấy giới thiệu hôn phối, đối tượng được giới thiệu sẽ được thêm vào danh sách giáo dân nếu người đó chưa có thông tin trong giáo xứ | While creating a marriage-introduction document, create the introduced person in the parishioner register if no record exists. | Marriage introduction / people | Rule — pending BUG-003 |
| CHG-112 | 3.1 | 147 | Chỉnh sửa "Trình độ văn hóa". | Education-level handling was changed, but no behavior or values are recorded. | People / reference data | Cosmetic/unspecified — closed |
| CHG-113 | 3.1 | 148 | Bổ sung "Trình độ chuyên môn", "Ngoại ngữ". | Store professional qualification and foreign-language information for a person. | People / profile | Rule — pending BUG-003 |
| CHG-114 | 3.1 | 149 | Chỉnh sửa hôn phối giáo dân. | Parishioner marriage handling was changed, but no behavior is recorded. | Marriage | Cosmetic/unspecified — closed |
| CHG-115 | 3.1 | 150 | Cho phép in phiếu lý lịch cá nhân hàng loạt | Allow batch generation of personal-profile sheets. | Reports / personal profile | Rule — pending BUG-003 |
| CHG-116 | 3.1 | 151 | Cho phép in phiếu lý gia đình hàng loạt | Allow batch generation of household sheets. | Reports / household sheet | Rule — pending BUG-003 |

## Duplicate and relationship index

Duplicates are retained because BUG-001 requires one row per recorded entry:

| Relationship | Rows | Treatment |
|---|---|---|
| Surviving-spouse status | CHG-027, CHG-039 | Same core rule stated in two releases; review together |
| Household-sheet marriage witnesses error | CHG-029, CHG-036 | Exact repeated fix; one future characterization behavior may cite both |
| Marriage-statistics names/sub-parish | CHG-031, CHG-037 | Exact repeated fix; one future characterization behavior may cite both |
| Household “has family” consistency | CHG-034, CHG-069 | Related symptoms in statistics and person detail; validate one domain invariant with separate UI outcomes |
| Household count exclusions | CHG-023, CHG-079, CHG-080, CHG-101 | Later entries refine role inclusion and exclusion conditions; do not collapse before domain review |
| Statistics printing/export | CHG-019, CHG-059, CHG-084 | Same result-availability concern across print and Excel export paths |
| Household-sheet output parity | CHG-086, CHG-087 | Template parity and option parity are distinct but should share one report contract |

## History-only rule candidates

The 99-commit legacy range ends at tagged release commit `360581d`. It begins
with the repository import and intentionally excludes `3887a7c` (`Add AI
stuff`) and all modernization commits. Commit subjects and changed paths were
reviewed locally; credential-bearing diffs and database contents were not
printed or copied.

These behaviors are stated more specifically in history than in the changelog,
or are absent from it:

| ID | Commit evidence | English / implied rule | Module | Status |
|---|---|---|---|---|
| HIST-001 | `29134cc` | The account-management list must render the available account records correctly. | Accounts | Rule — pending BUG-003 |
| HIST-002 | `0c39c2c`, `82c2609` | Support the required introduction-document families: catechism study, parish transfer, marriage catechism, baptism, and confirmation. Exact availability and fields require report review. | Reports / introductions | Rule — pending BUG-003 |
| HIST-003 | `add5848` | A blank date cell in an otherwise valid Excel import is treated as missing data rather than causing the import to fail. | Import / Excel / partial dates | Rule — pending BUG-003 |
| HIST-004 | `f2d16ab` | After deleting a person or household, refresh the relevant list without an error or stale deleted row. | People / households / lists | Rule — pending BUG-003 |
| HIST-005 | `c94feff` | Day-and-month inputs use partial-date validation and must not be rejected merely because no year exists. | Partial dates / validation | Rule — pending BUG-003 |
| HIST-006 | `d09b37b` | Find-and-replace can target address data. Authorization, preview, and audit behavior require review. | People / bulk edit | Rule — pending BUG-003 |
| HIST-007 | `7a56e10` | Legacy full dates were formatted `dd.MM.yyyy`; the modern display/export format requires explicit locale review. | Dates / presentation | Rule — pending BUG-003 |
| HIST-008 | `9d96c33` | Father and mother selectors remain usable when their text fields already contain information. | People / parents | Rule — pending BUG-003 |
| HIST-009 | `9460452`, `96b13b1` | Do not delete a parishioner who is referenced as a member, husband, or wife of a household; require the relationship to be resolved first. | People / households / integrity | Rule — pending BUG-003 |
| HIST-010 | `9460452` | When assigning parents, validate that each parent's age/date is older than the child's. Partial and unknown birth dates need an approved comparison policy. | People / parents / validation | Rule — pending BUG-003 |
| HIST-011 | `96b13b1` | Refresh both active and archived household/person lists after delete, transfer out, or transfer back. | Transfers / lists | Rule — pending BUG-003 |
| HIST-012 | `314e700` | Generate a unique identity code during Excel import when the source identity-code cell is blank. | Import / identity | Rule — pending BUG-003 |
| HIST-013 | `98d68c5` | Load catechism students in ascending class order/sequence. The sort key requires review. | Catechism / class | Rule — pending BUG-003 |
| HIST-014 | `c0d4f08` | Personal-profile and First Communion introduction reports must bind each value to the correct labeled field. | Reports / field mapping | Rule — pending BUG-003 |
| HIST-015 | `2a03e46`, `00dc94c`, `b1e4492`, `29aa49f` | Print every eligible household member, adding rows/pages when the member count exceeds the template's original seven/eight-row area. | Reports / household sheet | Rule — pending BUG-003 |
| HIST-016 | `eddf527` | Display person/household photos without distorting their aspect ratio. | People / households / photos | Rule — pending BUG-003 |
| HIST-017 | `7d4e236` | Include each household member's gender when required by the household-sheet contract. | Reports / household sheet | Rule — pending BUG-003 |
| HIST-018 | `588bd3d` | Selecting a person or household from sacrament detail must load the selected record, not stale or unrelated picker state. | Sacraments / people / households | Rule — pending BUG-003 |
| HIST-019 | `1b970ca`, `b21699b` | Person selection for a catechism class must accept the chosen eligible parishioner; sacrament detail also offers an authorized route to edit that person's record. | Catechism / sacraments / people | Rule — pending BUG-003 |
| HIST-020 | `ca250b4` | If an MGC marriage references a spouse not yet found in imported people, create/reconcile the person before creating the marriage relationship. | Import / MGC / marriage | Rule — pending BUG-003 |
| HIST-021 | `9299226` | Provide the approved bann-results (`Kết quả rao hôn phối`) report and its required fields. | Reports / banns | Rule — pending BUG-003 |
| HIST-022 | `229bf9d` | The marriage list may show a derived years-married value; calculation date, partial dates, and rounding require review. | Marriage / list | Rule — pending BUG-003 |
| HIST-023 | `b91db2e` | Changing sub-parish must handle an absent/null selection without crashing and leave a valid state. | Parish hierarchy / validation | Rule — pending BUG-003 |
| HIST-024 | `b91db2e` | Importing spouses must reconcile duplicates rather than violating a unique key or creating duplicate people/relationships. | Import / marriage / identity | Rule — pending BUG-003 |
| HIST-025 | `3fc4973` | Date import and grid/text binding must handle blank/null source values without a null-reference failure. Exact accepted date forms require profiling. | Import / dates | Rule — pending BUG-003 |
| HIST-026 | `77b65bf`, `0a7cbc2` | Store and display Anointing of the Sick information alongside the other supported sacrament/catechism details. | People / sacraments | Rule — pending BUG-003 |

## Git history traceability ledger

`Mapped` means the commit is represented by one or more rule rows.
`Non-behavioral` means it changes packaging, generated output, help, repository
hygiene, merge state, version metadata, or an implementation detail without a
separately testable user outcome. Aggregate commits remain linked to the
closest recorded release rows rather than being discarded.

| # | Commit | Subject | Classification and rule mapping |
|---:|---|---|---|
| 001 | `3d41697` | first commit | Non-behavioral: repository baseline import |
| 002 | `9af0049` | Update something | Mapped aggregate: CHG-099 and CHG-110 account/security introduction; the subject is too broad for another independent rule |
| 003 | `d88759c` | Final Update | Non-behavioral: removed temporary/unused source and project artifacts |
| 004 | `d162cc3` | Fix | Non-behavioral: generated-output and project cleanup; subject provides no testable outcome |
| 005 | `29134cc` | Fix lỗi hiển thị danh sách account | New: HIST-001 |
| 006 | `3ef45e5` | Update Help | Non-behavioral: help/account documentation and related cleanup |
| 007 | `08570cc` | Final | Mapped aggregate: CHG-040 and CHG-085–CHG-100; broad subject cannot isolate a further rule |
| 008 | `0c39c2c` | Final Final | New: HIST-002 |
| 009 | `82c2609` | Final | Mapped: HIST-002; template/report refinements |
| 010 | `0b2ad48` | change 07/02/2018 | Mapped aggregate: CHG-085–CHG-116; broad snapshot with no independently named fix |
| 011 | `a593993` | Prepare for merge with master | Mapped aggregate: CHG-085–CHG-116; merge preparation provides no unique rule |
| 012 | `ece8b38` | Final | Non-behavioral: IDE-state-only change |
| 013 | `62c5a6f` | Remove backup folder | Non-behavioral: repository hygiene |
| 014 | `3412fb9` | Commit for version 3.2.1 | Mapped aggregate: CHG-085–CHG-095 |
| 015 | `e387f70` | Commit missing file for version 3.2.1 | Mapped: CHG-112 and CHG-113; also restores project/package files |
| 016 | `1fe7a9c` | hihi | Mapped aggregate: CHG-064 and CHG-067 association management |
| 017 | `49233c3` | King | Mapped aggregate: CHG-064 and CHG-067 association membership/history |
| 018 | `c2a175f` | Hiep commit form HoiDoan | Mapped aggregate: CHG-064 and CHG-067 association forms/history |
| 019 | `cbd4044` | delete folder BIN | Mapped aggregate: CHG-053 and CHG-064–CHG-080; the misleading subject combines source changes with output cleanup |
| 020 | `0c8047e` | delete folder BIN | Non-behavioral: tracked build/runtime asset removal |
| 021 | `dc8005e` | release lan 1 | Non-behavioral: release snapshot |
| 022 | `d53c7f7` | create index in table ChuyenXu | Non-behavioral: legacy schema/performance implementation; no distinct user rule stated |
| 023 | `add5848` | Sửa lỗi import dữ liệu từ Excel thất bại khi có dữ liệu ngày tháng để trống. | New: HIST-003; related to CHG-009 and CHG-062 |
| 024 | `08e4162` | Cập nhật thông tin version | Non-behavioral: version metadata |
| 025 | `de1414c` | Xóa thư mục Backup | Non-behavioral: repository hygiene |
| 026 | `ce60272` | Xóa các thư mục không cần thiết | Non-behavioral: repository hygiene |
| 027 | `5096653` | Thêm icon và chỉnh sửa tiêu đề của hộp thoại cập nhật phiên bản mới tự động. | Non-behavioral: cosmetic change to the retired updater |
| 028 | `8bb8d3d` | commit Source and delete folder obj and BIN | Mapped aggregate: CHG-064–CHG-084; source snapshot plus generated-output cleanup |
| 029 | `85c1d8f` | Merge branch 'HiepBranch' of https://github.com/khoannd/qlgx into HiepBranch | Non-behavioral: merge commit with no independent diff |
| 030 | `dc9b202` | commit Source and delete folder BIN and obj | Non-behavioral: project membership/generated-output cleanup |
| 031 | `a1d6caa` | push late Source to merge in branch master | Mapped aggregate: CHG-047–CHG-052 household-list work; no unique subject detail |
| 032 | `564e25e` | commit Source to merge Branch Master | Mapped aggregate: CHG-062 import work plus merge/update artifacts |
| 033 | `8ab0fc5` | test | Mapped aggregate: CHG-062 import work; temporary backup tree removal is non-behavioral |
| 034 | `f8f8453` | test | Non-behavioral: merge commit with no independent diff |
| 035 | `0dcf8c0` | Hiep Merge between branch master and branch HiepBranch | Non-behavioral: removal of tracked local backup archives |
| 036 | `8475741` | delete file *.pdb and GiaoXu.mdb | Non-behavioral: removal of generated/debug and local database artifacts |
| 037 | `f2d16ab` | fix error in form GiaDinh and form GiaoDan when load data after delete | New: HIST-004 |
| 038 | `8de73b5` | sua loi in phieu gia dinh truong hop in nhieu gia dinh | Mapped: CHG-047 and CHG-116 |
| 039 | `8e9b04c` | doi ngay bon mang chi con ngay thang | Mapped: CHG-053 |
| 040 | `c94feff` | fix contraint GxDayMonthField | New: HIST-005; related to CHG-053 |
| 041 | `d09b37b` | add find and replace using address filed | New: HIST-006 |
| 042 | `7a56e10` | fix format date format(dd.MM.yyyy) | New: HIST-007 |
| 043 | `54ba701` | gach ten cua nguoi nam hoac nguoi nua khi qua doi o form gia dinh | Mapped: CHG-048 |
| 044 | `9d96c33` | khi nhap thong tin giao dan khong chon duoc cha hoac me khi textbox cua cha hoac me da co thong tin | New: HIST-008 |
| 045 | `9460452` | Khong duoc xoa giao dan khi giao dan do co ton tai trong 1 gia dinh khac va check tuoi cua cha me giao dan khi them phai lon hon giao dan | New: HIST-009 and HIST-010 |
| 046 | `96b13b1` | reload gird danh sach gia dinh khi xoa hoac chuyen xu di hoac chuyen xu ve trong ca 2 form gia dinh luu tru va gia dinh. Rang buoc khong duoc xoa giao dan khi giao dan do la thanh vien hoac la nguoi nam, nguoi nu cua mot gia dinh khac. | New: HIST-009 and HIST-011 |
| 047 | `314e700` | them ma nhan dang khi import du lieu bang excel khi du lieu cot ma nhan dang trong file excel trong | New: HIST-012 |
| 048 | `2a03e46` | Sua tinh nang in phieu gia dinh truong hop so luong thanh vien lon hon 7 | New: HIST-015; related to CHG-033 and CHG-094 |
| 049 | `00dc94c` | them thong tin rao hon phoi, giao ho nam va nu khi xuat chung nhan hon phoi,them thong tin them suc va dia chi cho nguoi nam va nguoi nu cho file template chung khi xuat chung nhan hon phoi, set table gioi thieu chuyen xu khi gia dinh co nhieu hon 7 nguoi | Mapped: CHG-054 and HIST-015 |
| 050 | `d990e0a` | update template ChungNhanHonPhoi cho cac giao xu | Mapped: CHG-054; diocese template variants remain for report inventory |
| 051 | `0926730` | Cho phep them sua thong tin CMND khi khong co hinh dai dien, cho xoa hoc vien lop giao ly, cho phep sua so thu tu hoc vien lop giao ly | Mapped: CHG-055, CHG-060, and CHG-061 |
| 052 | `98d68c5` | sua template tab thanh none va khi load hoc vien lop giao ly luon tang dan | New: HIST-013; template tab changes are non-behavioral |
| 053 | `77e7f29` | fix chon top 1 khi select gia dinh | Non-behavioral: legacy query/migration implementation with no stated user outcome |
| 054 | `5d533b8` | merge file template | Non-behavioral: merge commit with no independent diff |
| 055 | `da54b72` | merge HiepBranch voi master version 3.3.2 | Mapped aggregate: CHG-036–CHG-044 |
| 056 | `c0d4f08` | sua loi them giao dan khi dang filter bi exception, format va sua loi fill thong tin sai vi tri cua template lilichcanhan va XTRl | Mapped: CHG-032; new: HIST-014 |
| 057 | `4f6a667` | - Cho phép in Số bí tích trong mẫu in Phiếu Gia đình bằng Word - Đổi mẫu in Phiếu Gia đình bằng Word sang A3 | Mapped: CHG-090; A3/Word choice is obsolete implementation pending report-layout review |
| 058 | `89f415b` | update version for DB file | Non-behavioral: blank seed/schema release artifact; contents were not inspected |
| 059 | `b1e4492` | In tat ca thanh vien trong gia dinh khi gia dinh nhieu hon 8 nguoi. | New: HIST-015 |
| 060 | `0adf717` | cho phep reset avatar giao dan va gia dinh, gach bo nhung nguoi ngoai xu va khong thong ke | Mapped: CHG-023 and CHG-024 |
| 061 | `6293b1e` | sua loi du su kien cua button | Mapped: CHG-022; duplicate UI event wiring has no independent modern rule |
| 062 | `c2e7ef0` | Them button xem gia dinh trong form giao dan | Mapped: CHG-022 |
| 063 | `da62088` | sap xep lai cac button o form giao dan | Non-behavioral: UI arrangement |
| 064 | `c56ae44` | them gia dinh tu man hinh hon phoi | Mapped: CHG-025 |
| 065 | `eddf527` | Sửa mẫu in phiếu gia đình Sửa lỗi khi giáo dân có hôn phối nhưng mục đã có gia đình không checked Sửa lỗi khi giáo dân qua đời thì bỏ mục đã có gia đình của người vợ/chồng Sửa lại cách hiển thị hình ảnh cho không bị méo hình. | Mapped: CHG-026, CHG-027, and CHG-034; new: HIST-016 |
| 066 | `29aa49f` | in phieu gia dinh khi hon 8 nguoi thi se them dong voi template cot tuy y (khong co dinh so cot cua template) | New: HIST-015 |
| 067 | `c3caece` | Remove unnecessary file | Non-behavioral: output cleanup |
| 068 | `0ec0173` | First commit for installation package from version 3.3.3 | Non-behavioral: installer/update package |
| 069 | `a0f50e9` | Release phiên bản 3.3.3 | Mapped aggregate: CHG-021–CHG-035; release metadata otherwise non-behavioral |
| 070 | `7d4e236` | them gioi tinh vao template phieu gia dinh (2 mau excel va word) | New: HIST-017 |
| 071 | `588bd3d` | sua loi import file MGC SyntaxError, thay doi cach load giao dan va gia dinh khi chon giao dan va gia dinh tu form bi tich chi tiet | Mapped: CHG-015; new: HIST-018 |
| 072 | `95a41f5` | tim giao dan them 2 thuoc tinh ngay sinh va dia chi | Mapped: CHG-016 and CHG-057 |
| 073 | `1b970ca` | sua loi khong chon duoc giao dan trong lop giao ly, cho phep chinh sua giao dan o man hinh bi tich chi tiet | New: HIST-019 |
| 074 | `ca250b4` | thay the file giaoxu.mdb trong resource, Import MGC add giao dan khi khong tim thay giao dan trong hon phoi | New: HIST-020; seed replacement is a non-behavioral release artifact |
| 075 | `c02d70e` | Không disable nút "In danh sách" trong màn hình thống kê. | Mapped: CHG-059 |
| 076 | `b21699b` | Sửa lỗi không chọn giáo dân vào màn hình lớp giáo lý. Sửa giao diện màn hình chọn giáo dân. | New: HIST-019; picker layout is non-behavioral |
| 077 | `67ed530` | Release version 3.3.4 | Mapped aggregate: CHG-012–CHG-020; release metadata otherwise non-behavioral |
| 078 | `84c638d` | Add release package for 3.3.4 | Non-behavioral: installer/update package |
| 079 | `88d74f9` | Sửa lỗi: không hiển thị tên vợ chồng trong màn hình thống kê hôn phối. | Mapped: CHG-011 |
| 080 | `9299226` | add template KQRaoHonPhoi | New: HIST-021 |
| 081 | `229bf9d` | them cot so nam hon phoi vao danh sach hon phoi | New: HIST-022 |
| 082 | `b91db2e` | Sửa lỗi null object ref khi thay đổi giáo họ. Sửa lỗi duplicate key khi import vợ chồng. | New: HIST-023 and HIST-024 |
| 083 | `3fc4973` | Sửa một số lỗi import ngày tháng và null exception | New: HIST-025; exact source cases remain to be profiled |
| 084 | `77b65bf` | Thêm BT Xức dầu, GL Bao đồng, GL Hôn Nhân | Mapped: CHG-008; new: HIST-026 |
| 085 | `0a7cbc2` | Thêm thông tin bao đồng. Thêm thông tin xức dầu. Và sửa một số lỗi. | Mapped: CHG-008 and HIST-026; unspecified fixes add no rule |
| 086 | `1dff4eb` | cố gắng sử dụng nhiều loại connection string khi kết nối Excel data. Release 3.3.6 | Mapped: CHG-009; connection-string fallback is obsolete implementation |
| 087 | `4b82d98` | thêm thông tin cập nhật | Non-behavioral: update/release information |
| 088 | `dc8f159` | Add files via upload | Non-behavioral for rule register: report artifact without a stated behavior; retained for report inventory |
| 089 | `0f876dc` | Add try catch to import function | Non-behavioral: implementation hardening without a stated recovery/user outcome |
| 090 | `e4ba558` | Delete uncommitted files | Non-behavioral: repository cleanup |
| 091 | `0b6e02c` | Merge branch 'master' of https://github.com/khoannd/qlgx | Non-behavioral: merge commit with no independent diff |
| 092 | `ae8afff` | - Hiển thị thêm thông tin qua đời. - Hiển thị Tên Cha, Tên Mẹ, Ngày XTRL vào danh sách học sinh giáo lý | Mapped: CHG-001 and CHG-002 |
| 093 | `a0ee88d` | Fix error when import data from MGC | Mapped: CHG-003 and CHG-015; subject does not identify another rule |
| 094 | `9e68b8a` | Thêm thông tin tên người vợ/chồng vào mẫu in Lý lịch cá nhân | Mapped: CHG-005 |
| 095 | `bf710fb` | Chỉnh sửa chức năng import dữ liệu từ phần mềm MGC để có thể import dữ liệu cho nhiều giáo xứ khác nhau trong cùng một file DataGx.accdb | Mapped: CHG-004 |
| 096 | `e6b74cd` | Cho phép nhập ngày bí tích sau ngày hiện tại. Chỉ hiện cảnh báo khi lưu dữ liệu giáo dân. | Mapped: CHG-006 |
| 097 | `985a85d` | Release version 3.3.7 | Mapped aggregate: CHG-001–CHG-007; seed/version update otherwise non-behavioral |
| 098 | `215c218` | Release version 3.3.7 | Non-behavioral: installer/update package |
| 099 | `360581d` | Release version 3.3.7 | Non-behavioral: release-help metadata; marks the reviewed history boundary |

## BUG-003 review contract

For every `Rule — pending BUG-003` row, the domain reviewer must record:

1. `Still required`, `Changed`, or `Obsolete`;
2. the approved Vietnamese business statement and English equivalent;
3. a concrete valid example and error/edge example;
4. which roles may view or perform the behavior;
5. the source fields and partial/unknown states involved;
6. the future characterization-test ID, or a reason no automated rule applies.

Review related rows together but never delete their source traceability. A
changed rule keeps the original wording and adds the approved replacement.

## Completion checks

| Check | Result |
|---|---|
| XML bullet lines | 116 |
| Register rows | 116 (`CHG-001` through `CHG-116`) |
| Source versions/releases | 12 dated or named release sections represented |
| Unclassified rows | 0 |
| Current-policy decisions made by BUG-001 | 0; all behavioral rows remain pending BUG-003 |
| Legacy commits reviewed by BUG-002 | 99 (`3d41697` through `360581d`) |
| History-only rule candidates | 26 (`HIST-001` through `HIST-026`), all pending BUG-003 |
| Unmapped legacy commits | 0; every commit is mapped to rule rows/aggregate rows or explicitly non-behavioral |
